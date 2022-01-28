DataTable사용중 ajax로 받아온 data를 function 에 넣어야하는 경우가 생겼다. 
아래는 DataTable의 일부 코드이다. 

```
{
  targets: [6],
  render: function (data, type, row) {
      return '<div style = text-align:center;">\n' +
                '<a href="#" style="color:darkorange" data-toggle="modal" data-id="'+ data.id +'"\n' +
 		'data-title="' + data.name + '" data-target="#editModal" onclick = \'editBtn('+ data +')\'>수정</a></div>'
                }
            },
```
funtion (data, tyep, row)의 파라메터는 각각 ajax를 통해 가져온 데이터가 있다.
이 데이터에서 id와 name을 꺼내 html에 각각 넣고,
editBtn이라는 함수의 파라메터로는 data전체를 보내는 것이 목표였다.

>그러나 파라메터가 Object 로 넘어가기 때문에 editBtn함수가 실행되지 않는 것이 문제였다.

### Json Object를 js parameter로 사용하기


object를 stringify를 해주어 문자열로 바꿔주어야합니다. 
```userData = JSON.stringify(data);```

또한 주의점이 하나 더 있다면 html태그에 변수를 넣을 땐 '   ' 로 감싼 후 변수를 넣어줘야 했지만, 
stringify된 문자열은 이미 **그 자체가 문자열이기때문에** 변수가 아닌 문자열 넣듯이 넣어주어야 합니다. 
즉, **'   ' 로 감싸줄 필요없이**  + 기호를 이용해 html태그 사이에 stringify한 문자열을 넣어주면 됩니다. 

>받는 함수에서는 stringify가 json포멧으로 들어오므로, data.XXX와 같이 
오브젝트 처럼 사용하면됩니다. 

```
{
  targets: [6],
  render: function (data, type, row) {
                    userData = JSON.stringify(data);
          return '<div style = text-align:center;">\n' +
                  '<a href="#" style="color:darkorange" data-toggle="modal" data-id="'+ data.id +'"\n' +
                   'data-title="' + data.name + '" data-target="#editModal" onclick = \'editBtn('+ userData +')\'>수정</a></div>'
                }
            },
 ```
            
_쌍따옴표와 홑따옴표를 구분하는데도 애를 많이 먹었다. 쌍따옴표와 홑따옴표, \ (이스케이프) 문자 등을 잘 구별해서 사용해야겠다. _
