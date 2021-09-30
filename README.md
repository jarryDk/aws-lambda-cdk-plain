# Simplest Possible AWS Lambda Function with Cloud Development Kit (CDK) Boilerplate

A lean starting point for building, testing and deploying AWS Lambdas with Java.

# TL;DR

A simple Java AWS Lambda without any AWS dependencies:

```java

public class Greetings{

    public String onEvent(Map<String, String> input) {
        System.out.println("received: " + input);
        return input
        .entrySet()
        .stream()
        .map(e -> e.getKey() + "->" + e.getValue())
        .collect(Collectors.joining(","));
    }
    
}

```

deployed with AWS Cloud Development Kit for Java and Maven:


```java

import software.amazon.awscdk.services.lambda.Code;
import software.amazon.awscdk.services.lambda.Function;
import software.amazon.awscdk.services.lambda.Runtime;

//...

Function createUserListenerFunction(String functionName,String functionHandler, int memory, int timeout) {
    return Function.Builder.create(this, id(functionName))
            .runtime(Runtime.JAVA_11)
            .code(Code.fromAsset("../target/function.jar"))
            .handler(functionHandler)
            .memorySize(memory)
            .functionName(functionName)
            .timeout(Duration.seconds(timeout))
            .build();
}

```

with maven and cdk:

```
mvn clean package
cd cdk && mvn clean package && cdk deploy
```

System Tests included:

```java

 @BeforeEach
    public void initClient() {
        var credentials = DefaultCredentialsProvider
        .builder()
        .profileName("airhacks.live")
        .build();
        
        this.client = LambdaClient.builder()
                       .credentialsProvider(credentials)
                       .build();
    }

    @Test
    public void invokeLambdaAsynchronously() {
            String json = "{\"user \":\"duke\"}";
            SdkBytes payload = SdkBytes.fromUtf8String(json);

            InvokeRequest request = InvokeRequest.builder()
                    .functionName("airhacks_lambda_greetings_boundary_Greetings")
                    .payload(payload)
                    .invocationType(InvocationType.REQUEST_RESPONSE)
                    .build();

            var response = this.client.invoke(request);
            var error = response.functionError();
            assertNull(error);
            var value = response.payload().asUtf8String();
            System.out.println("Function executed. Response: " + value);
    }    

```



## AWS SDK

SDK used for System Testing

https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide