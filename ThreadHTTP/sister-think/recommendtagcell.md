# recommendTagCell

- 1. 在这个cell里边学会了重写cell的`- (void)setFrame:(CGRect)frame`
    - 这样的话cell的上下, 宽高就能微调了

```objc

- (void)setFrame:(CGRect)frame
{
    frame.origin.x = 5;
    frame.size.width -= 2 * frame.origin.x;
    frame.size.height -= 2;
    frame.origin.y += 2;

    [super setFrame:frame];
}

```
