(I'm not fluent in English. If someone correct my English I'd appreciate.)  

# AnimationBuilder: Easy way of writing Kivy Animations

Using kivy.animation.Animation directly is a pain. AnimationBuilder provides you with easy way.

## Usage

### Basic

```python
from kivy.garden.animationbuilder import AnimationBuilder


anims = AnimationBuilder.load_string(r'''
move_to_right:
  right: 800
  d: 2
move_to_top:
  top: 600
  t: in_out_cubic
''')

anims['move_to_right'].start(some_widget1)
anims['move_to_top'].start(some_widget2)
```

The code above is equivalent to  

```python
from kivy.animation import Animation

Animation(right=800, d=2).start(some_widget1)
Animation(top=600, t='in_out_cubic').start(some_widget2)
```

The former looks even worse than the latter. But when you write more complex animations, the latter becomes unreadable.  

### Sequential Animation

```yaml
test_sequential:
  sequential:
    - right: 800
      d: 2
    - top: 600
      t: in_out_cubic
```

You can use other animations inside.

```yaml
move_to_right:
  right: 800
  d: 2
test_sequential:
  sequential:
    - move_to_right
    - top: 600
      t: in_out_cubic
```

### Parallel Animation

```yaml
move_to_right:
  right: 800
  d: 2
test_parallel:
  parallel:
    - move_to_right
    - top: 600
      t: in_out_cubic
```

### Nest as you like

```yaml
more_nesting:
  parallel:
    - sequential:
        - opacity: 0
          d: 0.3
          t: in_out_quad
        - opacity: 1
          d: 0.3
          t: in_out_quad
      repeat: True
    - sequential:
        - right: 800
        - top: 600
        - x: 0
        - y: 0
        - d: 1
      repeat: True
```

But the code below might be easier to read.  

```yaml
more_nesting:
  parallel:
    - keep_moving
    - blinking

blinking:
  sequential:
    - opacity: 0
      d: 0.3
      t: in_out_quad
    - opacity: 1
      d: 0.3
      t: in_out_quad
  repeat: True

keep_moving:
  sequential:
    - right: 800
    - top: 600
    - x: 0
    - y: 0
    - d: 1
  repeat: True
```

### Freestyle

The example above is equivalent to this:  

```yaml
more_nesting:
  freestyle: "blinking & keep_moving"

blinking:
  sequential:
    - opacity: 0
      d: 0.3
      t: in_out_quad
    - opacity: 1
      d: 0.3
      t: in_out_quad
  repeat: True

keep_moving:
  sequential:
    - right: 800
    - top: 600
    - x: 0
    - y: 0
    - d: 1
  repeat: True
```

You can write Python expression with limited operators('+', '&', '()').  

#### sleep()

In a freestyle expression, you can use sleep() function  

```yaml
test:
  freestyle: "move_to_right + sleep(1) + move_to_top"

move_to_right:
  right: 800

move_to_top:
  top: 600
```

which is equivalent to  

```yaml
test:
  sequential:
    - move_to_right
    - d: 1
    - move_to_top

move_to_right:
  right: 800

move_to_top:
  top: 600
```

## Live Preview

Just like [kviewer](https://github.com/kivy/kivy/blob/master/kivy/tools/kviewer.py), livepreview.py allowing you to dynamically display the animation.

```text
python3 ./livepreview.py filename.yaml
```

![screenshot](screenshot/livepreview.png)  


## Requirements

- pyyaml
- watchdog (optional, only needed if you wanna use livepreview.py)

## Notes

### AnimationBuilder can not change the Animation parameters dynamically

So if you wanna do that, you need to do something like this:  

```
AnimationBuilder.load_string(''.format())
```

which is a huge disadvantage IMO.  

### Every time you call __getitem__(), it create a new instance of Animation

which means  

```python
anims['key'] is anims['key']
```

is always False.  

## Others

[Youtube](https://www.youtube.com/playlist?list=PLNdhqAjzeEGiF1oLISnCCPoPj1FhZbOAP)  

**Tested Environment**  
Python 3.5.0  
Kivy 1.10.0  
