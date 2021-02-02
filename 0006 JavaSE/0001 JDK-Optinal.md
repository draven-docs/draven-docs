# JDK-Optional

@since 1.8

```java
/**
orElse �õ��������ڵĶ���
orElseGet �õ���Supplier�ӿڷ��صĶ��� supplier�ӿھ�һ��get������
����Σ�����Ҫ��Optional�Ķ���ͬ���͡�
orElseThrow �õ���Supplier�ӿڷ��صĶ�������������Ҫʵ��Throwable�� supplier�ӿھ�һ��get������ ����Σ�����Ҫʵ��Throwable.

public T orElse(T other)

Return the value if present, otherwise return other.

Parameters:
    other - the value to be returned if there is no value present, may be null
Returns:
    the value, if present, otherwise other 
    
    
    
    
public T orElseGet(Supplier<? extends T> other)

Return the value if present, otherwise invoke other and return the result of that invocation.

Parameters:
    other - a Supplier whose result is returned if no value is present
Returns:
    the value if present otherwise the result of other.get()
Throws:
    NullPointerException - if value is not present and other is null 
        
        
public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier)
                                    throws X extends Throwable

Return the contained value, if present, otherwise throw an exception to be created by the provided supplier.

API Note:
    A method reference to the exception constructor with an empty argument list can be used as the supplier. For example, IllegalStateException::new
Type Parameters:
    X - Type of the exception to be thrown
Parameters:
    exceptionSupplier - The supplier which will return the exception to be thrown
Returns:
    the present value
Throws:
    X - if there is no value present
    NullPointerException - if no value is present and exceptionSupplier is null
    X extends Throwable 





System.out.println(Optional.ofNullable("��Ʒ").orElse("���Ʒ"));
System.out.println(Optional.ofNullable(null).orElse("���Ʒ"));


// �б���Ͳ�����·
System.out.println(Optional.ofNullable("����").orElseGet(()->"��·")); 
// û�������������г�
System.out.println(Optional.ofNullable(null).orElseGet(()->"���г�"));
// û����Ҳ������綯��
System.out.println(Optional.ofNullable(null).orElseGet(()->"�綯��"));  


// ��Ǯ��û�쳣
try {
    System.out.println(Optional.ofNullable("Ǯ").orElseThrow(()->new Exception()));  // ��Ǯ�������쳣
} catch (Throwable throwable) {
    throwable.printStackTrace();
}

// ûǮ�ͻ����쳣
try {
    System.out.println(Optional.ofNullable(null).orElseThrow(()->new Exception()));  // ûǮ���쳣
} catch (Throwable throwable) {
    throwable.printStackTrace();
}


*/
```



```java
Optional<T> filter(Predicate<? super T> predicate)

<U> Optional<U> map(Function<? super T,? extends U> mapper)
    
<U> Optional<U> flatMap(Function<? super T,Optional<U>> mapper)
```

