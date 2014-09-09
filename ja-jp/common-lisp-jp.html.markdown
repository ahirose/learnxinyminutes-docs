---

language: "Common Lisp"
filename: commonlisp.lisp
contributors:
  - ["Paul Nathan", "https://github.com/pnathan"]
---

ANSI Common Lisp は、様々な分野の工業アプリケーションに使える、
汎用的なマルチパラダイムプログラミング言語です。
プログラム可能なプログラミング言語として、よく知られています。

Lispを始める人向けのクラシック文書はこちら[Practical Common Lisp(無料で読めます)](http://www.gigamonkeys.com/book/)

新しいものとしてはこちら[Land of Lisp](http://landoflisp.com/).



```scheme

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;;; 0. 文法
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

;;; 一般的な形

;; Lispの文法には、二つの基礎的な要素があります。アトムとS式です
;; 一般的に、まとまったS式をフォームといいます

10  ; アトムです; これ自身を評価します

:THING ;別のアトムです; シンボルの :thing を評価します

t  ; 別のアトムです。trueを意味します

(+ 1 2 3 4) ; S式です

'(4 :foo  t)  ;別のS式です


;;; コメントです

;; コメント行は、セミコロン;一つで始まります。使い分けとしては、二つ使うと普通のコメントで、
;; 三つはセクションコメント、四つはファイルレベルでのコメントとされます

#| ブロックコメントです。
   複数行をコメントとして扱う事ができます、また、
    #|
       入れ子にもできます！
    |#
|#

;;; 環境

;; 様々な実装が存在しており、ほとんどは標準に準拠したものです。
;; 最初は CLISP がよいと思います

;; ライブラリは、Quicklisp.orgのQuicklispシステムで管理されています。

;; Common Lisp の開発は、テキストエディタと
;; REPL（Read Evaluate Print Loop、対話型評価環境) を同時につかって行います。
;; REPLを使うと、プログラムの実行とソースの確認を同時に、双方向でやることができます。


;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;;; 1. プリミティブ型と演算子
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

;;; シンボル

'foo ; =>FOOが返ります。シンボルは自動的に大文字になります。

;; internは、文字列からシンボルを作ります

(intern "AAAA") ; => AAAA

(intern "aaa") ; => |aaa|

;;; 数字
9999999999999999999999 ; 整数
#b111                  ; 2進数(binary) => 7
#o111                  ; 8進数(octal) => 73
#x111                  ; 16進数(hexadecimal) => 273
3.14159s0              ; 単精度（single）
3.14159d0              ; 倍精度（double）
1/2                    ; 比率
#C(1 2)                ; 複素数


;; 関数は (f x y z ...) のように書きます
;; f が関数で、x,y,z, ... は演算対象です
;; 値を評価せずに、そのまま文字リストとして見たい場合は、' を使うと、
;; データの引用(quote)ができます
'(+ 1 2) ; => (+ 1 2)
;; 関数を指定して、呼び出すことができます
(funcall #'+ 1 2 3) ; => 6
;; 計算用の演算子
(+ 1 1)              ; => 2
(- 8 1)              ; => 7
(* 10 2)             ; => 20
(expt 2 3)           ; => 8
(mod 5 2)            ; => 1
(/ 35 5)             ; => 7
(/ 1 3)              ; => 1/3
(+ #C(1 2) #C(6 -4)) ; => #C(7 -2)

                     ;;; ブーリアン
t                    ; true (not-nil 値はすべて true)
nil                  ; falseと、空リスト
(not nil)            ; => t
(and 0 t)            ; => t
(or 0 nil)           ; => 0

                     ;;; 文字
#\A                  ; => #\A
#\λ               ; => #\GREEK_SMALL_LETTER_LAMDA
#\u03BB              ; => #\GREEK_SMALL_LETTER_LAMDA

;;; 文字列は、文字の固定長配列
"Hello, world!"
"Benjamin \"Bugsy\" Siegel"   ; バックスラッシュは、エスケープ文字です

;; 文字列は、結合できます
(concatenate 'string "Hello " "world!") ; => "Hello world!"

;; 文字列は、連続した文字としても取り扱えます
(elt "Apple" 0) ; => #\A

;; formatは文字列の整形に使えます
(format nil "~a can be ~a" "strings" "formatted")

;; 表示は簡単です。~% で改行を指定できます
(format t "Common Lisp is groovy. Dude.~%")

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; 2. 変数
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; defパラメータで、グローバル（動的スコープ）の変数が作れます
;; 変数名には、 ()",'`;#|\ 以外の文字を使えます

;; 動的スコープ変数は、変数名に*耳当て*が必要です

(defparameter *some-var* 5)
*some-var* ; => 5

;; ユニコード文字も使えます
(defparameter *AΛB* nil)


;; 事前に指定をされていない変数にアクセスした場合の挙動は定義されていないので、やらないでください。
;; 一応可能ではあります


;; ローカルバインディングについて。
;; 以下で、`me` は "dance with you" に、(let ...)内でのみ、バインドされます
;; letは、常にletフォーム内の最終`form`を返します

(let ((me "dance with you"))
  me)
;; => "dance with you"

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; 3. 構造体とコレクション
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

;; 構造体
(defstruct dog name breed age)
(defparameter *rover*
    (make-dog :name "rover"
              :breed "collie"
              :age 5))
*rover* ; => #S(DOG :NAME "rover" :BREED "collie" :AGE 5)

(dog-p *rover*) ; => t  ;; ewww)
(dog-name *rover*) ; => "rover"

;; Dog-pと、make-dogと、dog-nameは、すべてdefstructによってつくられたものです

;;; ペア
;; `cons' はペアを作ります
;; `car' と `cdr' は、最初と二つ目の要素を抽出します
(cons 'SUBJECT 'VERB) ; => '(SUBJECT . VERB)
(car (cons 'SUBJECT 'VERB)) ; => SUBJECT
(cdr (cons 'SUBJECT 'VERB)) ; => VERB

;;; リスト

;; リストは、データが結合した構造をもっているものです
;; `cons'ペアがつながってできており、`nil' (または '()) を、最後尾の目印とします
(cons 1 (cons 2 (cons 3 nil))) ; => '(1 2 3)
;; `list' コンストラクタで、簡単にリストを作ることができます
(list 1 2 3) ; => '(1 2 3)
;; シングルクォートは、文字のリストを作るために使えます
'(1 2 3) ; => '(1 2 3)

;; `cons' は、リストの最初に値を追加するためにも使えます
(cons 4 '(1 2 3)) ; => '(4 1 2 3)

;; `append' （結合）は、--まったく予想外なのですが-- リストを結合します
(append '(1 2) '(3 4)) ; => '(1 2 3 4)

;; または、concatenateもつかえます

(concatenate 'list '(1 2) '(3 4))

;; リストはとても重要な型なので、それ用の関数がいろいろとあります
;; 例は以下です:
(mapcar #'1+ '(1 2 3))             ; => '(2 3 4)
(mapcar #'+ '(1 2 3) '(10 20 30))  ; => '(11 22 33)
(remove-if-not #'evenp '(1 2 3 4)) ; => '(2 4)
(every #'evenp '(1 2 3 4))         ; => nil
(some #'oddp '(1 2 3 4))           ; => T
(butlast '(subject verb object))   ; => (SUBJECT VERB)


;;; ベクター

;; ベクターは、固定長の配列として書きます
#(1 2 3) ; => #(1 2 3)

;; ベクター同士を結合するのには、concatenate をつかいます
(concatenate 'vector #(1 2 3) #(4 5 6)) ; => #(1 2 3 4 5 6)

;;; 配列

;; ベクターや文字列は、配列の一つです

;; 2次元配列

(make-array (list 2 2))

;; (make-array '(2 2)) としても大丈夫です

; => #2A((0 0) (0 0))

(make-array (list 2 2 2))

; => #3A(((0 0) (0 0)) ((0 0) (0 0)))

;; 注意-初期のデフォルト値は、処理系の実相によって異なります
;; 定義する場合は、以下のようにします

(make-array '(2) :initial-element 'unset)

; => #(UNSET UNSET)

;; 1,1,1 の要素にアクセスするためには、以下のようにします
(aref (make-array (list 2 2 2)) 1 1 1)

; => 0

;;; 可変ベクター

;; 可変ベクターは、固定長ベクターと同じような定数として書きます

(defparameter *adjvec* (make-array '(3) :initial-contents '(1 2 3)
      :adjustable t :fill-pointer t))
      
*adjvec* ; => #(1 2 3)

;; 要素の追加:
(vector-push-extend 4 *adjvec*) ; => 3

*adjvec* ; => #(1 2 3 4)



;;; 単純ですが、セットはリストです:

(set-difference '(1 2 3 4) '(4 5 6 7)) ; => (3 2 1)
(intersection '(1 2 3 4) '(4 5 6 7)) ; => 4
(union '(1 2 3 4) '(4 5 6 7))        ; => (3 2 1 4 5 6 7)
(adjoin 4 '(1 2 3 4))     ; => (1 2 3 4)

;; But you'll want to use a better data structure than a linked list
;; for performant work!

;;; Dictionaries are implemented as hash tables.

;; Create a hash table
(defparameter *m* (make-hash-table))

;; set a value
(setf (gethash 'a *m*) 1)

;; Retrieve a value
(gethash 'a *m*) ; => 1, t

;; Detail - Common Lisp has multiple return values possible. gethash
;; returns t in the second value if anything was found, and nil if
;; not.

;; Retrieving a non-present value returns nil
 (gethash 'd *m*) ;=> nil, nil

;; You can provide a default value for missing keys
(gethash 'd *m* :not-found) ; => :NOT-FOUND

;; Let's handle the multiple return values here in code.

(multiple-value-bind
      (a b)
    (gethash 'd *m*)
  (list a b))
; => (NIL NIL)

(multiple-value-bind
      (a b)
    (gethash 'a *m*)
  (list a b))
; => (1 T)

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; 3. Functions
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

;; Use `lambda' to create anonymous functions.
;; A function always returns the value of its last expression.
;; The exact printable representation of a function will vary...

(lambda () "Hello World") ; => #<FUNCTION (LAMBDA ()) {1004E7818B}>

;; Use funcall to call lambda functions
(funcall (lambda () "Hello World")) ; => "Hello World"

;; Or Apply
(apply (lambda () "Hello World") nil) ; => "Hello World"

;; De-anonymize the function
(defun hello-world ()
   "Hello World")
(hello-world) ; => "Hello World"

;; The () in the above is the list of arguments for the function
(defun hello (name)
   (format nil "Hello, ~a " name))

(hello "Steve") ; => "Hello, Steve"

;; Functions can have optional arguments; they default to nil

(defun hello (name &optional from)
    (if from
        (format t "Hello, ~a, from ~a" name from)
        (format t "Hello, ~a" name)))

 (hello "Jim" "Alpacas") ;; => Hello, Jim, from Alpacas

;; And the defaults can be set...
(defun hello (name &optional (from "The world"))
   (format t "Hello, ~a, from ~a" name from))

(hello "Steve")
; => Hello, Steve, from The world

(hello "Steve" "the alpacas")
; => Hello, Steve, from the alpacas


;; And of course, keywords are allowed as well... usually more
;;   flexible than &optional.

(defun generalized-greeter (name &key (from "the world") (honorific "Mx"))
    (format t "Hello, ~a ~a, from ~a" honorific name from))

(generalized-greeter "Jim")   ; => Hello, Mx Jim, from the world

(generalized-greeter "Jim" :from "the alpacas you met last summer" :honorific "Mr")
; => Hello, Mr Jim, from the alpacas you met last summer

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; 4. Equality
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

;; Common Lisp has a sophisticated equality system. A couple are covered here.

;; for numbers use `='
(= 3 3.0) ; => t
(= 2 1) ; => nil

;; for object identity (approximately) use `eql`
(eql 3 3) ; => t
(eql 3 3.0) ; => nil
(eql (list 3) (list 3)) ; => nil

;; for lists, strings, and bit-vectors use `equal'
(equal (list 'a 'b) (list 'a 'b)) ; => t
(equal (list 'a 'b) (list 'b 'a)) ; => nil

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; 5. Control Flow
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

;;; Conditionals

(if t                ; test expression
    "this is true"   ; then expression
    "this is false") ; else expression
; => "this is true"

;; In conditionals, all non-nil values are treated as true
(member 'Groucho '(Harpo Groucho Zeppo)) ; => '(GROUCHO ZEPPO)
(if (member 'Groucho '(Harpo Groucho Zeppo))
    'yep
    'nope)
; => 'YEP

;; `cond' chains a series of tests to select a result
(cond ((> 2 2) (error "wrong!"))
      ((< 2 2) (error "wrong again!"))
      (t 'ok)) ; => 'OK

;; Typecase switches on the type of the value
(typecase 1
  (string :string)
  (integer :int))

; => :int

;;; Iteration

;; Of course recursion is supported:

(defun walker (n)
  (if (zerop n)
      :walked
      (walker (1- n))))

(walker) ; => :walked

;; Most of the time, we use DOLIST or LOOP


(dolist (i '(1 2 3 4))
  (format t "~a" i))

; => 1234

(loop for i from 0 below 10
      collect i)

; => (0 1 2 3 4 5 6 7 8 9)


;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; 6. Mutation
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

;; Use `setf' to assign a new value to an existing variable. This was
;; demonstrated earlier in the hash table example.

(let ((variable 10))
    (setf variable 2))
 ; => 2


;; Good Lisp style is to minimize destructive functions and to avoid
;; mutation when reasonable.

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; 7. Classes and Objects
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

;; No more Animal classes, let's have Human-Powered Mechanical
;; Conveyances.

(defclass human-powered-conveyance ()
  ((velocity
    :accessor velocity
    :initarg :velocity)
   (average-efficiency
    :accessor average-efficiency
   :initarg :average-efficiency))
  (:documentation "A human powered conveyance"))

;; defclass, followed by name, followed by the superclass list,
;; followed by slot list, followed by optional qualities such as
;; :documentation.

;; When no superclass list is set, the empty list defaults to the
;; standard-object class. This *can* be changed, but not until you
;; know what you're doing. Look up the Art of the Metaobject Protocol
;; for more information.

(defclass bicycle (human-powered-conveyance)
  ((wheel-size
    :accessor wheel-size
    :initarg :wheel-size
    :documentation "Diameter of the wheel.")
   (height
    :accessor height
    :initarg :height)))

(defclass recumbent (bicycle)
  ((chain-type
    :accessor chain-type
    :initarg  :chain-type)))

(defclass unicycle (human-powered-conveyance) nil)

(defclass canoe (human-powered-conveyance)
  ((number-of-rowers
    :accessor number-of-rowers
    :initarg :number-of-rowers)))


;; Calling DESCRIBE on the human-powered-conveyance class in the REPL gives:

(describe 'human-powered-conveyance)

; COMMON-LISP-USER::HUMAN-POWERED-CONVEYANCE
;  [symbol]
;
; HUMAN-POWERED-CONVEYANCE names the standard-class #<STANDARD-CLASS
;                                                    HUMAN-POWERED-CONVEYANCE>:
;  Documentation:
;    A human powered conveyance
;  Direct superclasses: STANDARD-OBJECT
;  Direct subclasses: UNICYCLE, BICYCLE, CANOE
;  Not yet finalized.
;  Direct slots:
;    VELOCITY
;      Readers: VELOCITY
;      Writers: (SETF VELOCITY)
;    AVERAGE-EFFICIENCY
;      Readers: AVERAGE-EFFICIENCY
;      Writers: (SETF AVERAGE-EFFICIENCY)

;; Note the reflective behavior available to you! Common Lisp is
;; designed to be an interactive system

;; To define a method, let's find out what our circumference of the
;; bike wheel turns out to be using the equation: C = d * pi

(defmethod circumference ((object bicycle))
  (* pi (wheel-size object)))

;; pi is defined in Lisp already for us!

;; Let's suppose we find out that the efficiency value of the number
;; of rowers in a canoe is roughly logarithmic. This should probably be set
;; in the constructor/initializer.

;; Here's how to initialize your instance after Common Lisp gets done
;; constructing it:

(defmethod initialize-instance :after ((object canoe) &rest args)
  (setf (average-efficiency object)  (log (1+ (number-of-rowers object)))))

;; Then to construct an instance and check the average efficiency...

(average-efficiency (make-instance 'canoe :number-of-rowers 15))
; => 2.7725887




;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; 8. Macros
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

;; Macros let you extend the syntax of the language

;; Common Lisp doesn't come with a WHILE loop- let's add one.
;; If we obey our assembler instincts, we wind up with:

(defmacro while (condition &body body)
    "While `condition` is true, `body` is executed.

`condition` is tested prior to each execution of `body`"
    (let ((block-name (gensym)))
        `(tagbody
           (unless ,condition
               (go ,block-name))
           (progn
           ,@body)
           ,block-name)))

;; Let's look at the high-level version of this:


(defmacro while (condition &body body)
    "While `condition` is true, `body` is executed.

`condition` is tested prior to each execution of `body`"
  `(loop while ,condition
         do
         (progn
            ,@body)))

;; However, with a modern compiler, this is not required; the LOOP
;; form compiles equally well and is easier to read.

;; Note that ``` is used, as well as `,` and `@`. ``` is a quote-type operator
;; known as quasiquote; it allows the use of `,` . `,` allows "unquoting"
;; variables. @ interpolates lists.

;; Gensym creates a unique symbol guaranteed to not exist elsewhere in
;; the system. This is because macros are expanded at compile time and
;; variables declared in the macro can collide with variables used in
;; regular code.

;; See Practical Common Lisp for more information on macros.
```


## Further Reading

[Keep moving on to the Practical Common Lisp book.](http://www.gigamonkeys.com/book/)


## Credits.

Lots of thanks to the Scheme people for rolling up a great starting
point which could be easily moved to Common Lisp.

- [Paul Khuong](https://github.com/pkhuong) for some great reviewing.
