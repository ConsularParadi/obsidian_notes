
1. AND  &
2. OR  |
3. NOT  ~
4. XOR  ^

![[bitwise table.png]]

> [!example] 
> How to print ~2
> 2 = 10 -> 4 bytes (32 bits) -> 000...010
> ~2 = 111...101 (**1's complement**)
>-> 1 at start signifies negative number
> 000..010 (Again take 1's complement) + 1 (**2's complement**) -> 000...011
> ==-3==

5. Left Shift (<<) & Right Shift (>>)

> [!example] 
> x << y
> - x : integer on which shifting is applied
> - y : no. of times shifting is applied

> [!tip] 
> - Left shift -> multiplies by 2
> - Right shift -> divides by 2

> [!tip]  
> - Left shift for large number may result in negative number
> - Padding is always with zero 
> - Padding for negative number depends on compiler

6. Increment/Decrement (++, --)
- post -> use and then update (i++, i--)
- pre -> update and then use (++i, --i)

#### For Loop

> [!info] 
> for (initialization; condition; updation) 

#### Operator Precedence
![[operator precedence c++.png]]