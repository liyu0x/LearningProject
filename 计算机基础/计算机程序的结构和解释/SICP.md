# LISP

###### 1. 求1+1:

    (+ 1 1)

###### 2. 定义: 

    (Define a (+ 1 1))

###### 3. 定义一个Procedure:

    (Define (square x) (* x x))

or

    (Define square (lambda (x) (* x x)))



###### 4. 定义一个abs过程:

<code>

    (
        define (abs x)
            (cond 
                ((< x 0) (- x))
                ((= x 0) 0)
                ((> x 0) x)
            ) 
            # cond 是if的语法糖
    )

</code>

###### 5. 实现亚历山大求平方根算法:

<code>

    #第一步 
    (DEFINE (TRY GUESS X)
        (IF (GOOD-ENOUGH? GUESS X)
            GUESS
            (TRY (IMPROVE GUESS X) X)
        )
    )

    # 补充过程
    (define (improve guess x)
        (average guess (/ x guess))
    )

    (define (good-enough? guess x)
        (< (abs (- (square guess) x)) .001)
    )
</code>

