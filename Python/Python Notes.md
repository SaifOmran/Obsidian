- `_var` is private variable will not appear in variables explorer.
- print can have multiple arguments
```Python
print("first line is",
x,
"second line",
y
)
```

```Python
x = y = z = 1 # 3 variable and same value
x, y, z = 10, "hello", 100 # each varialbe has it is own value
```

#### while..else
![[Pasted image 20260220134325.png]]
### for loop
#### for in range()
![[Pasted image 20260220135025.png]]
#### for in enumerate
![[Pasted image 20260220140206.png]]
#### for in enumerate
![[Pasted image 20260220140239.png]]
- `_` is just placeholder and we don't need it for further calculations.
- `break` -> exit loop
- `continue` -> skip this iteration and continue (once the interpreter see `continue` , it doesn't go to next lines, it starts new iteration)
- `pass` -> there is an error in specific case and we tell the interpreter to act as there is no error