노마드 코더 파이썬 학습
리스트에는 변경할 수 없는 리스트를 만들땐 [] 대신 ()를 사용한다.  -- > tuple 

{} --> 딕셔너리
제이슨처럼 표현

me = {
"name" : "seung",
"age" : 26,
"korean" : True,
"fav_food" ; ["Burger", "Pizza"]
}

딕셔너리에 추가하기
me["good"] = True
me = {
"name" : "seung",
"age" : 26,
"korean" : True,
"fav_food" ; ["Burger", "Pizza"],
"good" = True
}

___
Function
___
def say_hello():
  print("hi")

say_hello()

output : hi
______


def say_hello(who):
  print(who)

say_hello("me")

____
def plus(a,b):
  print (a+b)

plus(3,5)

def minus(a,b=0):
  if (a > b):
    print(a-b)
  else :
    print(b-a)

minus (5,8)

def say_hello(who = "anonymous"):
  print ("hello", who);
  print ("hello" + who);

say_hello("seung")
say_hello();

___
output 

8
3
hello seung
helloseung
hello anonymous
helloanonymous


____
keyword Arguments
파이썬은 인자의 순서를 신경쓸 필요가 없다. 
인자의 이름에 따라서 작동된다. 

def say_hello(name, age):
  return  f"hello{name} you are {age} years old ."

hello =  say_hello("seung",26)
  
print(hello)

output 
helloseung you are 26 years old .

___
