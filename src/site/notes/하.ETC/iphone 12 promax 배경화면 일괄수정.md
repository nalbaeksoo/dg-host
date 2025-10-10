---
{"dg-publish":true,"permalink":"/하.ETC/iphone 12 promax 배경화면 일괄수정/","dgPassFrontmatter":true,"noteIcon":""}
---

#### CreateDate : 2025-10-10

### GOAL
iphone 12 promax 에서 배경화면이 정사이즈가 아닌 경우 불필요하게 확대되는 경우가 있음. 해당 상황을 방지하기 위해 임의로 가로, 세로에 블러처리
### Tested Env, APPLIES TO:
iphone 12 promax 
macOS

### SOLUTION
```
mkdir -p output input

for f in input/*.jpg; do
magick \
\( "$f" -auto-orient -resize 1284x2778 -background black -gravity center -extent 1284x2778 -blur 0x40 \) \
\( "$f" -auto-orient -resize 1284x2778 \) \
-gravity center -compose over -composite \
"output/$(basename "$f")"
done
```

### REFERENCES
chatgpt

#iphone

