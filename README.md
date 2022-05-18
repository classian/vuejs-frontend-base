
### 애플리케이션의 빌드와 실행
```
npm install
npm run serve
```

### 브라우저에서 열기
Labs > 포트열기 > 8080 (혹은 다른 포트번호)
> Invalid Host Header 오류를 제거하기 위하여 vue.config.js 에 devServer 블록을 추가:

```
module.exports = {
  transpileDependencies: [
    'vuetify'
  ],

  devServer: {
    allowedHosts: ["https://8080-msaschool-vuejsfrontend-147fbrurg95.ws-us45.gitpod.io/"],  // Invalid Host Header 가 표시된 퍼블릭 도메인 주소
  }
}

```

### 상품목록을 만들기 위하여 Card 컴포넌트를 사용
* card 컴포넌트 사용 설명 URL:  https://vuetifyjs.com/en/components/cards/
* 샘플을 복사:

```

  <v-card
    :loading="loading"
    class="mx-auto my-12"
    max-width="374"
  >
    <template slot="progress">
      <v-progress-linear
        color="deep-purple"
        height="10"
        indeterminate
      ></v-progress-linear>
    </template>

    <v-img
      height="250"
      src="https://cdn.vuetifyjs.com/images/cards/cooking.png"
    ></v-img>

    <v-card-title>Cafe Badilico</v-card-title>

    <v-card-text>
      <v-row
        align="center"
        class="mx-0"
      >
        <v-rating
          :value="4.5"
          color="amber"
          dense
          half-increments
          readonly
          size="14"
        ></v-rating>

        <div class="grey--text ml-4">
          4.5 (413)
        </div>
      </v-row>

      <div class="my-4 subtitle-1">
        $ • Italian, Cafe
      </div>

      <div>Small plates, salads & sandwiches - an intimate setting with 12 indoor seats plus patio seating.</div>
    </v-card-text>

    <v-divider class="mx-4"></v-divider>

    <v-card-title>Tonight's availability</v-card-title>

    <v-card-text>
      <v-chip-group
        v-model="selection"
        active-class="deep-purple accent-4 white--text"
        column
      >
        <v-chip>5:30PM</v-chip>

        <v-chip>7:30PM</v-chip>

        <v-chip>8:00PM</v-chip>

        <v-chip>9:00PM</v-chip>
      </v-chip-group>
    </v-card-text>

    <v-card-actions>
      <v-btn
        color="deep-purple lighten-2"
        text
        @click="reserve"
      >
        Reserve
      </v-btn>
    </v-card-actions>
  </v-card>

```

- App.vue 의 "HelloWorld" 태그를 복사한 내용으로 대체
- 사용하지 않는 컴포넌트선언들을 주석처리하여 무력화:

```
<script>
//import HelloWorld from './components/HelloWorld';

export default {
  name: 'App',

  components: {
 //   HelloWorld,
  },

  data: () => ({
    //
  }),
};
</script>
```

### 상품목록을 위한 변수 선언
```
  data: () => ({
    products: []
  }),
```


### 생성자에 상품 목록 얻어오는 로직 작성
```
export default {
  name: 'App',

  components: {
 //   HelloWorld,
  },

  data: () => ({
    products: []
  }),

  async created() {
      var temp = await axios.get('http://8081-labs--1247012378.kuberman.io/products')

      console.log(temp.data._embedded.products)
      this.products = temp.data._embedded.products;
  }
}

```

> 쇼핑몰 상품 정보 서비스를 호출하기 위해서 다음의 git 에서 코드를 다운받고 실행시켜야 합니다:
```
git clone https://github.com/event-storming/monolith.git
cd monolith
mvn spring-boot:run
```



> axios 라이브러리를 가져오기 위하여   다음 import 문장을 export default 상단에 추가:   
```
const axios = require('axios').default;
```
> 그래도 라이브러리가 없다고 나오면 터미널에서 다음을 실행 (front-end 의 디렉토리에서 실행)
```
npm install axios
```

### 화면 요소와 데이터의 바인딩 (MVVM)
v-card 태그와 얻어온 product 데이터를 바인딩 합니다.
```
 <v-card v-for="(product, index) in products" v-bind:key="index" 
 ...
```
> 화면에 아까 존재하던 카드가 사라졌나요? 그렇다면 서버에 아직 product 상품이 존재하지 않기 때문입니다.

### 상품 서비스 기동시키기
새 터미널을 열고 monolith2micsvc 폴더의 monolith 프로젝트에 진입하여 백엔드를 기동시킵니다:
```
cd monolith2misvc/
cd monolith
mvn spring-boot:run
```
> 상품서비스의 외부접속 주소를 얻기 위하여 Labs>포트열기>8081 (모놀리스 프로젝트의 포트번호) 를 통하여 브라우저에서 외부 주소를 확인합니다. 확인한 외부주소를 복사하여 앞서의 ajax 코드에 반영해줍니다:

```
var temp = await axios.get('http://8081-labs--1247012378.kuberman.io/products')
```

* 화면을 리프래시 하여 상품개수만큼 리스트가 들어오는 것을 확인합니다

### 상품 이름 표시하기

상품이름을 어떻게 출력할 수 있을까요? vuejs 에서는 템플릿 html 내용에 {{변수명}} 를 사용하여 변수내용을 해당 위치에 표시할 수 있습니다.

```
    <v-card-title>{{product.name}}</v-card-title>
```

### 주문 목록으로 전환하기

상품목록 대신 주문 목록을 표시하도록 전환해보겠습니다. 주문정보를 통해서 어떤 상품을 주문했는지와, 배송상태를 얻어와 하나의 카드에 같이 출력하도록 Lazy Loading 방식으로 UI 에서 각각의 다른 마이크로서비스에서의 데이터를 통합할 수 있습니다.

```
  data: () => ({
    orders: []
  }),

async created() {
      var temp = await axios.get('http://8081-labs--1247012378.kuberman.io/orders')

      console.log(temp.data._embedded.orders)
      this.orders = temp.data._embedded.orders;

      this.orders.forEach(async order=>{
          var productData = await axios.get('http://8081-labs--1247012378.kuberman.io/products/' + order.productId)

          order.product = productData.data;

          order.__ob__.dep.notify()
      })
  }
```

코드는 얻어온 주문의 목록에서 각 주문의 상품데이터를 다시 fetch 하여 객체에 담습니다. 주문정보에 상품정보가 보완이 되면, 그 때에 __ob__.notify() 에 의하여 객체의 변화가 MVVM 체계에 의하여 템플릿에 전달되어 변경된 주문 정보를 업데이트 하게 됩니다. 

따라서 템플릿 부분도 아래와 같이 변경합니다:

```
<v-card v-for="(order, index) in orders" v-bind:key="index"
    :loading="loading"
    class="mx-auto my-12"
    max-width="374"
  >
...

    <v-card-title>      
        <v-progress-linear
            v-if="!order.product"
        color="deep-purple"
        height="10"
        indeterminate
      ></v-progress-linear>
      <div v-else>
        {{order.product.name}}
      </div>
    </v-card-title >
```

v-if 를 통하여 order.product 의 정보가 로딩되는 중일때는 progress bar 를 표시하고, 주문정보가 채워질때 비로소 상품이름을 표시함으로서 각 마이크로서비스의 호출결과가 도달하지 않더라도 주문의 기본 정보는 미리 표시해 둘 수 있습니다.


### 확장 실험
해당 주문에 대한 배송상태 정보도 같이 표시해보세요.

### Order 를 위한 Vue Component 를 분리하시오

VueJS 는 도메인 특화된 html 태그를 만들 수 있습니다. 이것은 유비쿼터스 랭귀지를 유지하여 비즈니스 피플과 개발자간의 커뮤니케이션을 유지하는 효과와 동시에 향후 컴포넌트의 재사용성과 간섭의 분리효과도 같이 높힐 수 있습니다.  주문에 대한 화면과 내부 정보를 채우는 로직을 "ShoppingOrder" 라고 하는 Vue Component 로 분리하여 html 태그로 간편하게 호출하여 어디서든 활용할 수 있도록 만들어 보겠습니다.

먼저, 우리는 기존의 v-card 부분을 이렇게 변경하고 싶습니다:

```
          <shopping-order :order="order" v-for="(order, index) in orders" v-bind:key="index"/>
```
> :order="order" 는 order 라는 컴포넌트에 주문한 건의 데이터를 전달 한다는 컴포넌트간 데이터 바인딩입니다. ":"이 들어가면 뒤에 따르는 값은 문자열이 아닌 실행세션 내의 접근가능한 변수 참조가 됩니다.

그렇게 하기 위하여 기존의 v-card 의 템플릿영역과 관련된 로직의 덩어리들을 Order.vue 라고 하는 컴포넌트를 만들어서 사용하고 싶습니다. 기존에 HelloWorld.vue 라고 하는 샘플 컴포넌트를 참고하여 다음과 같이 ShoppingOrder.vue 를 만들어 components 폴더아래 위치합니다 (혹은 HelloWorld.vue 파일명을 변경해서 바로 작성해도 좋겠네요):


```
<template>
  <v-card 
    :loading="loading"
    class="mx-auto my-12"
    max-width="374"
  >
    <template slot="progress">
      <v-progress-linear
        color="deep-purple"
        height="10"
        indeterminate
      ></v-progress-linear>
    </template>

    <v-img
      height="250"
      src="https://cdn.vuetifyjs.com/images/cards/cooking.png"
    ></v-img>

    <v-card-title>      
        <v-progress-linear
            v-if="!order.product"
        color="deep-purple"
        height="10"
        indeterminate
      ></v-progress-linear>
      <div v-else>
        {{order.product.name}}
      </div>
    </v-card-title >

    <v-card-text >
      <v-row
        align="center"
        class="mx-0"
      >
        <v-rating
          :value="4.5"
          color="amber"
          dense
          half-increments
          readonly
          size="14"
        ></v-rating>

        <div class="grey--text ml-4">
          4.5 (413)
        </div>
      </v-row>

      <div class="my-4 subtitle-1">
        $ • Italian, Cafe
      </div>

      <div>Small plates, salads & sandwiches - an intimate setting with 12 indoor seats plus patio seating.</div>
    </v-card-text>

    <v-divider class="mx-4"></v-divider>

    <v-card-title>Tonight's availability</v-card-title>

    <v-card-text>
      <v-chip-group
        v-model="selection"
        active-class="deep-purple accent-4 white--text"
        column
      >
        <v-chip>5:30PM</v-chip>

        <v-chip>7:30PM</v-chip>

        <v-chip>8:00PM</v-chip>

        <v-chip>9:00PM</v-chip>
      </v-chip-group>
    </v-card-text>

    <v-card-actions>
      <v-btn
        color="deep-purple lighten-2"
        text
        @click="reserve"
      >
        
      </v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
  export default {
    name: 'ShoppingOrder',

    props: {
        order: Object
    },

    data: () => ({
    }),
  }
</script>

```
여기서 주목할 점은 props 부분입니다. 외부에서 이 컴포넌트를 사용할때 props 에서 정의된 attribute 를 통해서 inbound 로 받아올 값을 얻어올 수 있습니다. 템플릿은 기존의 코드 그대로를 옮겨왔습니다.

이 컴포넌트를 이용할 수 있도록 하려면 앞에서 주석처리했던 import 절과 component 목록에 Order 를 추가해주어야 합니다:

```
<script>

import ShoppingOrder from './components/ShoppingOrder'
...

export default {
  name: 'App',

  components: {
      ShoppingOrder
  },
	
	...
}

```

이렇게 변경한 후 브라우저를 리프래시하여 기존의 동작과 동일하게 동작하는지 확인해 봅니다. 

보시다시피, 상품정보를 읽어들여오지 못합니다. 이것은 한번 넘겨줄때의 order 값이 다른 컴포넌트에 전달될때는 해당 값의 복사본으로 전달되기 때문에 더이상 App.vue 에서 넘겨준 orders 각자의 값의 변경이 Order 컴포넌트 내부에는 영향을 줄 수 없기 때문에 발생한 문제입니다. 

다르게 생각하면, 이제부터는 Order.vue 에서 자신의 주문정보내부의 세부정보를 채우도록 하는 로직을 가져오는 것이 더 간섭과 Order.vue 파일을 관리할 주문팀의 자치성을 높히는 방향이 되므로, Order.vue 에 상품정보를 추가하는 로직을 옮겨오도록 하겠습니다.

기존 App.vue 에 있던 상품정보를 로딩하는 for loop 를 잘라내어 Order.vue 의  생성자 (created 메서드) 로 옮깁니다:

App.vue
```
 async created() {
      var temp = await axios.get('http://8081-labs--1247012378.kuberman.io/orders')

      console.log(temp.data._embedded.orders)
      this.orders = temp.data._embedded.orders;

      [이 부분이 잘라낼 부분]
			
  }
```

Order.vue
```
    async created(){

        var productData = await axios.get('http://8081-labs--1247012378.kuberman.io/products/' + this.order.productId)

        this.order.product = productData.data;

        this.order.__ob__.dep.notify()

    }
```

Order.vue 에서는 for loop 가 더이상 필요 없어집니다. 각자의 Order Component 에서 created 는 매번 호출 되기 때문이죠. 

그리고 기존에 그냥 "order" 로 접근했던 코드는 자신의 객체의 멤버 프로퍼티를 확실하게 참조하도록 "this.order"로 모두 변경해줍니다.

자, 이제 리프래시해서 기존 동작과 동일하게 동작됨을 확인합니다. 


이렇게 각 도메인별 컴포넌트를 분리하면 팀별 자율성을 높히면서 간섭없이 큰 프론트엔드를 관리할 수 있습니다. 

#### Order.vue 의 완성파일
vue compiler 의 오류검증이 강화됨에 따라 props 의 값을 직접 변경하는 것이 불가능해졌기 때문에 초기값만을 복사한 후, 세부 값을 변경하는 코드로 변경됨:
```
<template>
  <v-card 
    :loading="loading"
    class="mx-auto my-12"
    max-width="374"
  >
    <template slot="progress">
      <v-progress-linear
        color="deep-purple"
        height="10"
        indeterminate
      ></v-progress-linear>
    </template>

    <v-img
      height="250"
      src="https://cdn.vuetifyjs.com/images/cards/cooking.png"
    ></v-img>

    <v-card-title>      
        <v-progress-linear
            v-if="!innerOrder.product"
        color="deep-purple"
        height="10"
        indeterminate
      ></v-progress-linear>
      <div v-else>
        {{innerOrder.product.name}}
      </div>
    </v-card-title >

    <v-card-text >
      <v-row
        align="center"
        class="mx-0"
      >
        <v-rating
          :value="4.5"
          color="amber"
          dense
          half-increments
          readonly
          size="14"
        ></v-rating>

        <div class="grey--text ml-4">
          4.5 (413)
        </div>
      </v-row>

      <div class="my-4 subtitle-1">
        $ • Italian, Cafe
      </div>

      <div>Small plates, salads & sandwiches - an intimate setting with 12 indoor seats plus patio seating.</div>
    </v-card-text>

    <v-divider class="mx-4"></v-divider>

    <v-card-title>Tonight's availability</v-card-title>

    <v-card-text>
      <v-chip-group
        v-model="selection"
        active-class="deep-purple accent-4 white--text"
        column
      >
        <v-chip>5:30PM</v-chip>

        <v-chip>7:30PM</v-chip>

        <v-chip>8:00PM</v-chip>

        <v-chip>9:00PM</v-chip>
      </v-chip-group>
    </v-card-text>

    <v-card-actions>
      <v-btn
        color="deep-purple lighten-2"
        text
        @click="reserve"
      >
        
      </v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
  const axios = require('axios').default;

  export default {
    name: 'ShoppingOrder',

    props: {
        order: Object
    },

    data: () => ({
        innerOrder: Object
    }),

    async created(){

        this.innerOrder = this.order;
         
        var productData = await axios.get('https://8088-labs--1471950141.kuberez.io/products/' + this.order.productId)

console.log(productData);
        this.innerOrder.product = productData.data;

        this.innerOrder.__ob__.dep.notify()
    
    }
  }
</script>
```

### 마이크로 프론트엔드

이렇게 잘 만들어진 VueJS 컴포넌트구조가 있다하더라도 프론트엔드 컴포넌트들은 빌드를 통하여 한번에 반영되어야 하므로 실행중의 커플링은 여전히 존재하며, 각 팀이 만약 다른 프레임워크를 혼용하여 사용하길 선호한다면 (예를 들어 React.js 등) 여러분께서는 "마이크로프론트엔드"를 검토해볼 수 있습니다.  마이크로 프론트엔드는 다음과 같은 장점을 제공해 줍니다:

1. 팀간 분리된 컴포넌트 기반 개발과 통합
1. 팀간 배포 독립성
1. 팀간 기술 독립성 (다종의 UI-framework 혼합)
1. 장애 격리 (하나의 컴포넌트내의 자바스크립트 엔진이 죽어도 다른 컴포넌트가 영향 안받아야)
1. 이벤트 채널을 통한 컴포넌트간 연동 

마이크로 프론트엔드를 적용해보고 싶다면 다음의 링크들을 참고하세요:

- https://www.martinfowler.com/articles/micro-frontends.html
- https://micro-frontends.org/
- https://github.com/jinyoung/micro-frontends-with-web-components
- https://medium.com/javascript-in-plain-english/create-micro-frontends-using-web-components-with-support-for-angular-and-react-2d6db18f557a


#### 마이크로 프론트엔드 실습하기 위한 기본 코드 다운로드
```
```

#### 포트넘버 수정

micro-fe-ng/package.json 을 아래와 같이 수정:
```
    "start": "npm run build && serve -l 5001 dist/micro-fe-ng",

```
-->
```
    "start": "npm run build && serve -l 8081 dist/micro-fe-ng",
```

나머지 micro-fe-react, micro-fe-vue, micro-fe-wrapper 의 package.json 내의 포트넘버를 아래와 같이 변경:
```
5002 -> 8082
5003 -> 8083
5000 -> 8080
```

각 프로젝트를 Open in Terminal 을 통해 별도 터미널을 열고 
```
npm i
npm start
```

마지막으로, micro-fe-wrapper/index.html 을 다음과 같이 수정:

```

  <script src="https://8081-labs--1471950141.kuberez.io/main.js"></script>
  <script src="https://8082-labs--1471950141.kuberez.io/main.js"></script>
  <script src="https://8083-labs--1471950141.kuberez.io/order-form.js"></script>


```

Labs> 포트열기> 8080

