# pygame相关
pygame要记忆的东西其实也不少，下面列举一些常用的
 
## `pygame.locals`中的一些常用函数
### Rect
表示一个矩形
`Rect(x, y, width, height)`
```
+-----width----------+
|                    |
|               height
|                    |
+(x, y)--------------+
```

## 文字的显示
```
# Get font
pygame.font.init()
font = pygame.font.Font(None, 24)
text = font.render("Good job", True, (255,0,0))
# Position
textRect = text.get_rect()
textRect.centerx = screen.get_rect().centerx
textRect.centery = screen.get_rect().centery+24
screen.blit(text, textRect)
```