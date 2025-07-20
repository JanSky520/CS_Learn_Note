#+title:   计算机程序的构造和解释
#+author:  JanSky
#+date:    2025-05-25
#+STARTUP: overview indent


* 构造过程抽象
** 程序设计的基本元素
1. Lisp 的代码是由一堆括号来表达的，在计算法则中运用前缀表达式
   #+begin_src emacs-lisp
     (+ 1 2)       ; 3
     (- 5 3)       ; 5 - 3 = 2
     (* 2 4)       ; 2 * 4 = 8
     (/ 11 9)      ; 11 / 3 = 3 (会自动舍弃小数点)
     (/ 32 4 2)    ; 32 / 4 / 2 = 4
     (/ 11.0 3)    ; 11.0 / 3 = 3.6666666666666665 (运算数中带有小数点会自动进行浮点数运算)
   #+end_src
2. 可以用 setq 来定义一个全局的变量
   #+begin_src emacs-lisp
     (setq x 10)          ; 给变量 x 赋值为 10
     (setq y (* 3 x))     ; 3 * 10 = 30 赋值给 y
     (/ (* x y) 5)        ; 10 * 30 / 5 = 60
   #+end_src
3. 组合式的求值遵循递归过程，是过程抽象和数据抽象的基础
4. 过程定义是一种更加强大的抽象技术，可以为复杂的计算定义名字
   #+begin_src emacs-lisp
     (defun square (x)            ; 定义一个函数 square，负责平方操作
       (* x x))

     (defun sum-of-square (x y)   ; x^2 + y^2
       (+ (square x) (square y)))

     (defun f (x)
         (sum-of-square (+ x 1) (* x 2)))

     (square 5)                   ; 5 * 5 = 25
     (square (+ 1 2))             ; 9
     (sum-of-square 1 2)          ; 1^2 + 2^2 = 5
   #+end_src
5. 调用函数时将函数中的形参用实参替代，elisp 采用应用序求值，避免表达式重复求值
   #+begin_src emacs-lisp
     (defun add (a b)
       (+ a b))

     (add (* 2 3) (+ 1 2))  ; 先计算 (* 2 3) = 6 和 (+ 1 2) = 3，再调用 (add 6 3)
   #+end_src
6. 常见的条件表达式有 cond if not and or 这几种
   #+begin_src emacs-lisp
     (defun abs (x)             ; 求绝对值
       (cond                    ; cond 表达式
       ((> x 0) x)              ; x > 0 时取 x
       ((= x 0) 0)              ; x = 0 时取 0
       ((< x 0) (- x))))        ; x < 0 时取 -x

     (defun abs (x)          
       (cond                    ; cond 表达式的另一种写法
       ((> x 0) x)     
       ((= x 0) 0)              
       (t (- x))))              ; t 表示其余情况

     (defun abs (x)
       (if (< x 0)              ; 当 x < 0 的时候
       (- x)                    ; 取 -x
       x))                      ; 否则取 x

     (and nil (+ 1 2))          ; and 具有任意个数的参数，并从左到右对它们求值，有参数为假，那么它就返回假，而不对剩余参数求值
     (and 2 (+ 1 2))            ; 上一个表达式结果 nil。这个表达式结果为 3 因为上一个表达式第一个是 nil 直接终止返回 nil

     (or 3 nil 1)               ; or 具有可变个数的参数，并从左到右对它们求值。它返回第一个值不是假，而余下的参数不会被求值
     (or nil nil)               ; 上一个表达式结果为 3，因为第一个不是假，所以直接返回第一个参数，这个表达式两个为 nil 故返回 nil
     (not nil)                  ; 返回 t，因为 nil 的相反值是 t
     (not -1)                   ; 任何非 nil 的数都表示 t，故返回 nil
   #+end_src
7. 数学函数与过程之间的重要差异就是过程必须是有效可行的，在计算机中人们更关心行动性描述
   #+begin_src emacs-lisp
     (defun average (x y)
       (/ (+ x y) 2))
     (defun improve (guess x)
       (average guess (/ x guess)))
     (defun good-enough (guess x)
       (abs (- (* guess guess) x)))
     (defun sqrt-iter (guess x)
       (if (< (good-enough guess x) 0.01)
           guess
         (sqrt-iter (improve guess x) x)))
     (defun my-sqrt (x)
       (sqrt-iter 1.0 x))

     (my-sqrt 2)    ; 1.4166666666666665
   #+end_src
8. 关心其输入和输出，而不需要了解其内部实现细节。这种抽象方式有助于管理复杂性，构建模块化系统
9. 练习题答案
   #+begin_src emacs-lisp
     ; 练习 1
     ; 10 12 8 3 -6 3 4 nil 4 16 6 16

     ; 练习 2
     (/ (+ 5 4 (- 2 (- 3 (+ 6 (/ 4 5))))) (* 3 (- 6 2) (- 2 7)))

     ; 练习 3
     (defun max-two-sum (x y z)
       (cond
        ((> (+ x y) (+ x z) (+ z y)) (+ x y))
        ((> (+ x z) (+ x y) (+ z y)) (+ x z))
        (t (+ z y))))

     ; 练习 4
     ; 由 b 的值来判断是进行 + 计算还是 - 计算

     ; 练习 5
     ; 如果解释器无限递归是应用序求值，返回值是 0 则是正则序求值

     ; 练习 6
     ; 递归深度过深，强制中断过程

     ; 练习 7
     (defun good-enough (old-guess new-guess)
       (/ (abs (- new-guess old-guess)) old-guess))
     (defun sqrt-iter (guess x)
       (if (> 0.1 (good-enough guess (improve guess x)))
           (improve guess x)
         (sqrt-iter (improve guess x) x)))

     ; 练习 8
     (defun approximate (x y)
       (/ (+ (/ x (* y y)) (* 2 y)) 3))
     (defun cube-iter (x y)
       (cond
        ((< (abs (- (* y y y) x)) 0.001) y)
        (t (cube-iter x (approximate x y)))))
     (defun cube (x)
       (cube-iter x 1.0))
   #+end_src
** 过程及其产生的计算
1. 线性递归展开时形成递归调用链，依赖调用栈保存中间状态，最后收缩计算；线性迭代通过状态变量逐步更新结果，无调用栈累积
   #+begin_src emacs-lisp
     (defun fact-iter (product counter max-counter)
       (if (> counter max-counter)
           product
         (fact-iter (* product counter) (+ 1 counter) max-counter)))
     (defun factorial (n)
       (fact-iter 1 1 n))
   #+end_src 
2. 
3. 
4. 
5. 
6. 
7. 练习题答案
   #+begin_src emacs-lisp
     ; 练习 9
     ; (+ 4 5) -> (inc (+ 3 5)) -> (inc (inc (+ 2 5))) -> (inc (inc (inc (+ 1 5)))) -> (inc (inc (inc (inc (+ 0 5)))))
     ; (inc (inc (inc (inc 5)))) -> (inc (inc (inc 6))) -> (inc (inc 7)) -> (inc 8) -> 9
     ; (+ 4 5) -> (+ 3 6) -> (+ 2 7) -> (+ 1 8) -> (+ 0 9) -> 9
     ; 第一个是递归，第二个是迭代

     ; 练习 10
     ; 1024 65536 65536
     ; 2n 2^n 2^(2^n)
   #+end_src



