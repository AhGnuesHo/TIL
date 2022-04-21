파이썬에서 입력을 받을 때에는 input을 사용한다. 
// 값 두개를 입력받아 변수 a, b에 저장
a,b = input().split() # 문자열로 입력
a,b = map(int, input().split()) # 정수형으로 입력
a,b = list(float, input().split()) # 실수형으로 입력 

// 1차원 배열 입력받기
num_list = list(map(int, input().split())) 
// 입력 123
// 출력 [1,2,3]

//문자열 여러 줄 입력받기
n = (int)input(); // n줄 입력
n_list = [input() for _ range(n)]

// 문자열을 한 줄씩 입력받아서 한 글자씩 나누기
s = input()
for i in range(len(s)):
  sum += int(s[i])
  
// 띄어쓰기 없이 정수 여러개 입력받아 2차원 배열로 저장하기
two_d = [list(map(int, input()))) for _ in range(n)]



