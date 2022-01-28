>base64를 인코딩하여 사용하던 중 url safe가 되지않아 디코딩한 값이 깨지는 현상이 발생했다. 

### base64 
base64란 컴퓨터가 8비트의 이진데이터를 ASCII코드로 바꾸는 인코딩 방식입니다. 

다음은 base 64코드표입니다.
![](https://images.velog.io/images/nnoshel/post/14804cfa-087c-45ae-8bfc-c79022adcfd6/image.png)
 
base64는 이 64개의 글자를 이용해서 인코딩된 base64문자열을 생성합니다.  
또한 문장이 끝나면 '=' 로 데이터가 끝났다는 것을 표시해, 원복할때 틀리지 않도록 방지합니다. 

인코딩 순서는 아래와 같습니다. 
![](https://images.velog.io/images/nnoshel/post/9df5a267-079d-42c8-b8f0-463e6afa666a/image.png)

1. 텍스트를 하나씩 아스키 문자에 대응합니다. 
2. 아스키 코드를 8비트로 변환합니다. 
3. 나열된 8비트를 6비트씩 잘라서 십진수로 변환합니다. 
4. 이렇게 얻은 십진수를 base 64 코드표에 맞게 대응합니다. 
```
public static String encodeToString(Object obj) {
        Gson gson = new GsonBuilder().serializeNulls().create();
        String json = gson.toJson(obj);

        byte[] targetBytes = json.getBytes();
        return Base64.getEncoder().encodeToString(targetBytes);
    }
 ```
 위 코드는 오브젝트를 받아 Gson을 사용하여 json으로 파싱한 뒤, json을 base64로 인코딩한 예제입니다. 
 
 디코딩은 다음과 같습니다. 
  ```
   public static <T> T base64Decoding(String org, Class<T> type) {
        Base64.Decoder decoder = Base64.getDecoder();

        byte[] decodingData = decoder.decode(org);

        String strDecodingData;

        try {
            strDecodingData = new String(decodingData, "UTF-8");

        } catch (Exception e) {
            e.printStackTrace();
            throw new RuntimeException("decoding fail");
        }

        Gson gson = new Gson();

        return gson.fromJson(strOrgData, type);
    }
``` 


### base64 url safe
base64형태의 문자열을 웹으로 전송하는 경우 +(62),/(63) 글자가 포함되어있어 정상적으로 전송되지 않습니다. 
이
base64 url safe은 이 문제를 해결하기위해 등장했습니다. 
![](https://images.velog.io/images/nnoshel/post/cd451821-2144-41e4-b46f-27bf2abaadcc/image.png)
위의 표 처럼 -(62), _(63)으로 변경된 것을 확인 할 수있습니다. 

