---
{"dg-publish":true,"permalink":"/가.오라클/ETC/LCO invaild/","dgPassFrontmatter":true,"noteIcon":""}
---


### _CURSOR_RELOAD_FAILURE_THRESHOLD
```

Bug Reference: Bug 32959683 FA SAAS 19C: DROP PARTITION ON FND_SESSIONS WITH GLOBAL INDEXES HANGS ON LIBRARY CACHE LOCK
             Bug 33093428 - FA SAAS 19C: SET PARAMETER _CURSOR_RELOAD_FAILURE_THRESHOLD TO EFFECTED PODS Sev 1 SR
             Bug 33214177 - FA SAAS 19C: SET PARAMETER _CURSOR_RELOAD_FAILURE_THRESHOLD=20
             Enh 33214215 - FA SAAS 19C: SET PARAMETER _CURSOR_RELOAD_FAILURE_THRESHOLD=20

Issue:
1.) During high concurrency sql's on specific table, and table went for DDL operation, Which lead to invalidations of cursors.
2.) When invalidation and same time high concurrent sql's try to reparse the sql's lead to heavy contention on $BUILD$ area.
3.) And this lead to DEADLOCK situation, as per RDBMS in bug 12633340
        - In 11.2, we introduced the concept of cursor build locks as a mechanism for serializing the building of a child cursor under a given parent cursor.
        - This causes severe contention for the build lock in extreme scenarios when thousands of sessions concurrently try to parse the same SQL statement.
        - This manifests as waits for the build lock itself and also as library cache mutex contention (since the build lock is just a KGL lock).
        - When one of the sessions will manage to get the build lock in exclusive mode (while other sessions wait) and will build a child cursor
          that the other sessions can then share.
        - In this best case scenario, the contention will be very brief.
        - But in a scenario where reload failures are constantly occurring (the reason for the reload failure itself is not relevant here),
          the build lock and mutex contention will extend much longer.
        - Due to the way build locks are currently architected, sessions could get stuck in kksfbc() for long periods of time, fighting over the
          build lock and searching the child list over and over.
        - This would continue until some session finally manages to build a child cursor that everyone can share.
4.) If the number of reload failures in the last 5 minutes exceeds a certain threshold, then we will mark the child cursor as unusable
    and proceed to create a new one.
5.) The rationale is that if a certain number of sessions have tried and failed to reload a particular child cursor, then
    other sessions are also likely to fail and hence it's best to abandon it and move on.
6.) The default value for the threshold is the same as number of CPUs on the system.
 
Symtoms:
1.) "library cache lock" with in_parse=Y
2.) "library cache:mutex x" with in_parse=Y and in_hard_parse=Y
```

### ref MOS 
#### High Version Count Due To BIND_MISMATCH (Doc ID 336268.1)
```
SOLUTION
The fix of the bug 2450264 has introduced a new event (10503) which enables users to specify a character bind buffer length. Depending on the length used, the character  binds  in the child cursor can all be created using the same bind length;

ALTER system SET EVENTS '10503 trace name context forever, level 4000';, level <buffer length>';

Eg:
ALTER system SET EVENTS '10503 trace name context forever, level 4000';;
```

### case s전자
+ Library cahce lock 에 빠진 sessions 들의 library object lock / handle 을 보면, 대부분 namespacedump 내용의 child cursor 에 reason = Marked for Purge(3) 가 발생하는 것으로 보임. 해당 session 들은 모두 insert 문을 수행중 parse 과정에서 'library cache lock' 및 'mutext lock' 을 대기하여 blocking session 들이 발생하는 것으로 미루어 보면, 동시에 많은 session 들에서 동일 insert 문을 수행하는 환경으로 보임. 많은 Session 들에서 수행되는 SQL 에서 참조되는 table 에 DDL 문으로 인해 cursor invalidation 이 발생되는 경우 SQL 문을 parsing 하는 과정에서 cursor reload 실패가 반복적으로 발생되게 되는 경우 심한 library cache lock contention 이 야기 될 수 있으므로 _cursor_reload_failure_threshold parameter 를 사용하여 reload failure 횟수를 제한하는 방법도 'library cache lock' 을 완화시킬 수 있음.

+ 위의 Situation 에 대해서 더 확인시  다른 system / 19c 환경에서도 동일한 현상이 발생되어 진행된 case 들을 확인. 당시 분석되어 제안된 내용들은 다음과 같음.
1. gc current request' 와 관련된 known issue 들에 대한 fix 적용을 위해 Doc ID 2752049.1 의 merge patch 적용을 고려. 
   이미 모두 Fixed 되었음을 확인. Bug 30866988, 29337294, 29741319, 29129691, 30381614, 30473634, 30772069, 30978554, 32245850
2. Bug 33979028 - Similar issue 로 제안된 Bug 32922090, 32659930 적용. 해당 Patch 들은 기적용된 patch lists 에는 포함되지 않았음.
3. _cursor_reload_failure_threshold parameter 를 5 로 설정.

- 위 session들은 모두 동일한 insert 문을 수행중 parse 과정에서 'library cache lock' 및 mutext lock을 대기하고 있습니다. blocking session수가 많은 점에서 동시에 많은 세션들에서 동일 insert 문을 수행중인 환경으로 보여집니다. 이렇게 많은 세션들에서 수행되는 SQL문에서 참조되는 table에 DDL 문으로 인해 cursor invalidation이 발생되는 경우 SQL 문을 parsing 하는 과정에서 cursor reload 실패가 반복적으로 발생되게 되는 경우 심한 library cache lock contention이 야기 될 수 있습니다. bug 32991289 에서는 DDL 로 인하여 invalidation이 필요한 cursor에 대해 reuse 및 reload가 가능하도록 성능 개선이 이루어졌으니 성능 개선에 도움을 줄 수 있을 것으로 보입니다. 또 다른 방법으로 cursor_reload_failure_threshold parameter (현재는 0) 을 5 정도로 줄여 reload failure 횟수를 제한하는 것이 도움이 될 수 있겠습니다.

### tracking 방법
1. 모든 DB instance들의 ADR trace 밑에서 아래의 file들중 문제 시점에 발생된 메세지를 담고 있는 file들을 수집해주시기 바랍니다.

	*dia*.trc
	*lmhb*.trc
	*lmd*.trc

2. 동일 문제 재발시 다음 명령으로 hanganalyze dump를 수집해주십시요.

	SQL> oradebug setmypid
	SQL> oradebug -g all hanganalyze 3
	<약 15초 후>          
	SQL> oradebug -g all hanganalyze 3
	<약 15초 후>          
	SQL> oradebug -g all hanganalyze 3
	
	=> 모든 instance의 diag trace 에 dump가 기록됩니다.
	
	감사합니다.
 