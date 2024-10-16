---
layout: post
title: "Axon and Spring Security Integration"
date: 2024-10-05 11:43:01 +0700
categories: Axon Spring
---

# Axon Framework

[Axon Framework](https://axoniq.io) provides a simple but powerful way to build an event-driven application. It can work
with Spring perfectly. To integrate with Spring Security, we need 2 steps:

1. Create a dispatch interceptor to embed the jwt along with the message as a metadata
2. Create a handler interceptor to extract jwt from the message's metadata and convert it to an authentication

# Create a dispatch interceptor

Here, the jwt is extracted from SecurityContextHolder, and sent along with the message:

```java

@Component
@Slf4j
public class AxonMessageDispatchInterceptor implements MessageDispatchInterceptor<Message<?>> {
    @Nonnull
    @Override
    public BiFunction<Integer, Message<?>, Message<?>> handle(@Nonnull List<? extends Message<?>> messages) {
        return (index, message) -> {
            var accessToken = message.getMetaData().get(AxonMetaDataKeys.ACCESS_TOKEN);

            if (Objects.nonNull(accessToken)) {
                return message;
            }

            var authentication = SecurityContextHolder.getContext().getAuthentication();

            if (authentication instanceof JwtAuthenticationToken jwt) {
                var metaData = message.getMetaData().and(AxonMetaDataKeys.ACCESS_TOKEN, jwt.getToken().getTokenValue());

                return message.andMetaData(metaData);
            }

            return message;
        };
    }
}
```

# Create a handler interceptor

The security context is restored by reading the metadata:

```java

@Component
public class AxonMessageHandlerInterceptor implements MessageHandlerInterceptor<Message<?>> {
    @Autowired
    private JwtDecoder jwtDecoder;

    @Autowired
    private Converter<Jwt, AbstractAuthenticationToken> authenticationConverter;

    @Override
    public Object handle(
            @Nonnull UnitOfWork<? extends Message<?>> unitOfWork,
            @Nonnull InterceptorChain interceptorChain
    ) throws Exception {
        var accessToken = (String) unitOfWork.getMessage().getMetaData().get(AxonMetaDataKeys.ACCESS_TOKEN);

        if (accessToken != null) {
            var jwt = jwtDecoder.decode(accessToken);

            var authentication = this.authenticationConverter.convert(jwt);

            SecurityContextHolder.getContext().setAuthentication(authentication);
        }

        return interceptorChain.proceed();
    }
}
```