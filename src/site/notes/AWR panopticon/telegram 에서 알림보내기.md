---
{"dg-publish":true,"permalink":"/AWR panopticon/telegram 에서 알림보내기/","dgPassFrontmatter":true}
---



특정 페이지의 내용이 변할 때 알림을 받기 위해서는 다음과 같은 방법을 사용할 수 있습니다.

1.  BeautifulSoup 라이브러리를 사용하여 웹페이지의 HTML 코드를 파싱합니다.
2.  원하는 요소의 내용을 가져와 이전에 저장된 값과 비교합니다.
3.  값이 변경되었다면 Telegram API를 사용하여 알림을 보냅니다.

이를 파이썬 코드로 구현하면 다음과 같습니다.

pythonCopy code

```
import requests
from bs4 import BeautifulSoup
import time
import os
import telegram

# Telegram 봇 설정
bot = telegram.Bot(token='your_bot_token_here')
chat_id = 'your_chat_id_here'

# 웹페이지 URL 설정
url = 'your_url_here'

# 이전에 가져온 페이지 내용 저장
previous_content = None

while True:
    # 웹페이지의 HTML 코드 가져오기
    response = requests.get(url)
    soup = BeautifulSoup(response.content, 'html.parser')
    
    # 원하는 요소의 내용 가져오기
    content = soup.find('your_element_here').text.strip()
    
    # 이전 내용과 현재 내용 비교
    if previous_content is None:
        previous_content = content
    elif previous_content != content:
        # 내용이 변경되었다면 Telegram 알림 보내기
        bot.send_message(chat_id=chat_id, text='페이지 내용이 변경되었습니다!')
        # 변경된 내용 출력
        print('페이지 내용이 변경되었습니다!')
        # 현재 내용을 이전 내용으로 업데이트
        previous_content = content
    
    # 1분마다 페이지 내용 확인
    time.sleep(60)

```
`

위 코드에서 `your_bot_token_here`와 `your_chat_id_here`에는 본인이 생성한 Telegram 봇의 토큰과 알림을 받을 채팅방의 ID를 입력해주어야 합니다. `your_url_here`에는 확인하고자 하는 웹페이지의 URL을 입력해주시면 됩니다. `your_element_here`에는 페이지에서 확인하고자 하는 요소의 HTML 태그와 클래스 또는 ID 등을 입력해주시면 됩니다. 코드를 실행하면 1분마다 페이지 내용을 확인하고, 내용이 변경되면 Telegram으로 알림을 보내고 변경된 내용을 출력합니다.