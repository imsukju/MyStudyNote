### RESTful API의 기본 개념과 원칙

1. **리소스(Resource) 기반**
   - REST는 리소스를 기반으로 합니다. 리소스는 API에서 관리하려는 모든 대상(데이터 또는 객체)을 말합니다. 예를 들어, `users`, `orders`, `products` 등이 리소스가 될 수 있습니다.
   - 각 리소스는 고유한 URI(Uniform Resource Identifier)로 식별됩니다. 예를 들어, 특정 사용자의 정보를 조회할 때 `https://api.example.com/users/123`와 같은 URI를 사용할 수 있습니다.

2. **HTTP 메서드 사용**
   - RESTful API는 HTTP 메서드를 사용하여 리소스에 대한 작업을 정의합니다. HTTP 메서드는 클라이언트가 서버에 무엇을 하고자 하는지 명확하게 나타냅니다.
     - `GET`: 리소스 조회
     - `POST`: 리소스 생성
     - `PUT`: 리소스 전체 업데이트
     - `PATCH`: 리소스 부분 업데이트
     - `DELETE`: 리소스 삭제

   예를 들어:
   - `GET /users`: 모든 사용자 목록 조회
   - `GET /users/123`: 특정 ID(123)를 가진 사용자 조회
   - `POST /users`: 새로운 사용자 생성
   - `PUT /users/123`: ID 123의 사용자 정보 전체 업데이트
   - `DELETE /users/123`: ID 123의 사용자 삭제

3. **상태 비저장성(Stateless)**
   - RESTful API는 HTTP와 마찬가지로 **Stateless**(상태 비저장성) 특성을 가집니다. 서버는 각 클라이언트의 상태를 기억하지 않으며, 클라이언트가 요청을 보낼 때마다 모든 필요한 정보를 요청에 포함해야 합니다.
   - 각 요청은 독립적이며, 서버는 요청 간의 상태를 유지하지 않습니다. 이는 확장성과 단순성을 높이지만, 세션이나 사용자 상태를 관리할 필요가 있을 때는 추가적인 구현이 필요합니다(예: 쿠키 또는 토큰을 이용한 세션 관리).

4. **URI와 리소스**
   - URI는 리소스의 경로를 나타내며, RESTful API에서는 리소스마다 고유한 URI가 할당됩니다.
   - URI는 리소스를 나타내는 명사로 구성하는 것이 일반적입니다. 예를 들어, `GET /products/123`는 특정 상품의 정보를 요청하는 URI이며, `products`는 리소스를, `123`은 특정 리소스를 식별하는 고유 ID를 나타냅니다.

5. **표현(Representation)**
   - 클라이언트와 서버 간에 리소스가 전달될 때, 그 리소스는 다양한 형식으로 표현될 수 있습니다. 일반적으로 JSON(JavaScript Object Notation) 형식이 사용되며, XML도 사용될 수 있습니다.
   - 예를 들어, 서버가 `GET /users/123` 요청에 응답할 때, 해당 사용자의 정보를 JSON 형식으로 반환할 수 있습니다.

   ```json
   {
     "id": 123,
     "name": "John Doe",
     "email": "john.doe@example.com"
   }
   ```

6. **Self-descriptive Messages**
   - RESTful API의 각 메시지는 자체적으로 설명 가능해야 합니다. 즉, 요청에 필요한 모든 정보가 포함되어 있어야 하며, 메시지 자체로 그 의미를 파악할 수 있어야 합니다.
   - HTTP 상태 코드(200, 404, 500 등)와 함께 응답 메시지를 통해 서버는 요청이 성공했는지, 실패했는지를 나타낼 수 있습니다.

7. **HATEOAS (Hypermedia As The Engine Of Application State)**
   - RESTful 아키텍처에서 클라이언트는 서버가 제공하는 하이퍼미디어 링크를 통해 다음 행동을 결정할 수 있습니다. 즉, API의 응답에는 그 리소스와 관련된 추가 리소스나 행동으로 가는 링크가 포함될 수 있습니다.
   - 예를 들어, 사용자 정보 응답에 그 사용자의 주문 내역으로 이동할 수 있는 링크를 포함시킬 수 있습니다.

   ```json
   {
     "id": 123,
     "name": "John Doe",
     "links": [
       { "rel": "orders", "href": "/users/123/orders" }
     ]
   }
   ```

8. **캐시 가능성(Cacheability)**
   - RESTful API는 HTTP의 캐싱 메커니즘을 사용하여 서버와 클라이언트 간의 통신을 효율적으로 만들 수 있습니다. 서버는 각 응답에 대해 캐시 가능 여부를 명시하여 클라이언트가 이후 요청을 반복하지 않도록 할 수 있습니다.
   - 예를 들어, `GET` 요청에 대한 응답은 캐시할 수 있지만, `POST` 요청에 대한 응답은 캐시하지 않도록 설정할 수 있습니다.

### RESTful API의 장점
1. **확장성**
   - REST는 서버와 클라이언트 간의 분리를 명확하게 유지하며, 서버와 클라이언트의 개발이 독립적으로 이루어질 수 있습니다. 또한, Stateless 특성으로 인해 서버의 부하를 분산하거나 확장하기에 유리합니다.

2. **유연성**
   - RESTful API는 다양한 포맷(주로 JSON, XML)을 사용할 수 있으며, 이를 통해 다양한 플랫폼(웹, 모바일 등)에서 활용할 수 있습니다.

3. **표준화된 인터페이스**
   - HTTP 메서드와 상태 코드의 표준을 따르기 때문에, 다양한 시스템 간의 통신을 쉽게 할 수 있습니다. 이는 개발자들이 API를 쉽게 이해하고 활용할 수 있게 만듭니다.

### RESTful API의 단점
1. **복잡한 요청**
   - 일부 복잡한 기능을 구현하기 위해서는 여러 번의 요청을 주고받아야 할 수 있어, 시스템 전체적으로 오버헤드가 발생할 수 있습니다.
   
2. **처리 성능**
   - HTTP를 기반으로 동작하기 때문에, 응답 시간 및 대역폭 사용이 문제될 수 있으며, 이를 해결하기 위한 최적화 작업이 필요합니다.

결론적으로, RESTful API는 웹 애플리케이션에서 서버와 클라이언트 간의 통신을 효율적이고 일관되게 관리하기 위한 방식으로 널리 사용됩니다. HTTP의 장점을 최대한 활용하며, 확장성과 유연성을 제공하는 API 설계 방식입니다.