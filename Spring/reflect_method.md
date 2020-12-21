### invoke 예시
```
String[] test = {"VoField1", "VoField2", "VoField3"};

for(int i = 0; i < test.length; i++){

    TestVo vo = new TestVo();

    Class<?> class = vo.getClass();

    Method method = class.getMethod("set" + test[j], new Class[]{String.class});
    method.invoke(vo, request.getParameterValues("param" + test[j]));
}
```
