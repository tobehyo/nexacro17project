# Vue.js 사용 예제 설명

> vue.xfdl Form에서 vue.js를 사용하는 제한적인 예제입니다.  
> Web Runtime Envirment(Web browser) 에서만 사용이 가능하며 script tag 를 이용하여 vue.js 호출하여 사용합니다.  
> 이 예제에서는 ES2015 사양의 `const` [(참고 URL)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/const) 와 `Template Literals(``)`[(참고 URL)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Template_literals) 를 사용했습니다.

## Target Browser

Moder browser  
예: `Chrome`, `Firefox`, `Edge`, `Safari`, etc.  
`IE(Internet Explorer)` 제외

## 예제 설명

`vue_onload`

##### onload 이벤트에서 loadScript 함수를 호출

vue.js 로드, vue 에서 사용할 dom 을 생성 및 callback 함수에서 vue component 생성

```js
this.vue_onload = function (obj: nexacro.Form, e: nexacro.LoadEventInfo) {
  // WRE(Web Runtime Envirment) 여부 체크
  if (system.navigatorname.indexOf("nexacro") === -1) {
    // HTML 에 style 추가
    this.addCSS(cssStyle);

    // vue.js 경로
    const url = "https://unpkg.com/vue@next";

    // vue component tag, dom name, vue를 위한 dom object의 위치 참고를 위한 Nexacro object 전달
    const vueOption = {
      domName: "app",
      componentNames: [
        "<button-counter></button-counter>",
        "<button-counter></button-counter>",
      ],
      targetObjectForPosition: this.Static02,
    };

    // Vue.js 로드
    this.loadScript(url, this.callVueElement, vueOption);
  }
};
```

`loadScript`

##### vue 에서 사용할 dom 생성 및 vue.js 로드 후 callback 함수 호출

```js
this.loadScript = function (src, callback, option) {
  if (option) {
    // dom 생성
    this.createDom(option);
  }

  var script = document.createElement("script");
  script.setAttribute("src", src);
  // script 의 onload 이벤트에서 callback 함수 실행
  script.addEventListener("load", callback);
  document.head.appendChild(script);
};
```

`createDom`

##### vue 에서 사용할 dom 생성

```js
this.createDom = function (option) {
  const vueDiv = document.createElement("div");
  const domInfo = this.getRealDomInfo(option.targetObjectForPosition);
  const el = domInfo.element;
  const pos = domInfo.position;
  const top =
    domInfo.nexacroObj instanceof nexacro.Form
      ? pos.top
      : pos.top + pos.height + 10 + "px";

  vueDiv.id = option.domName;
  vueDiv.style.left = pos.left + "px";
  vueDiv.style.top = top;
  vueDiv.innerHTML = option.componentNames.join("");

  el.parentElement.appendChild(vueDiv);
};
```

`getDomId`

##### Nexacro object 의 dom id 가져오기

```js
this.getDomId = function (obj, useQuerySelector) {
  const id = obj._unique_id;
  if (useQuerySelector) id = "#" + obj._unique_id.replace(/\./g, "\\.");
  return id;
};
```

`getRealDomInfo`

##### vue 에 mount 할 dom object 의 위치 지정을 위한 정보 가져오기

```js
this.getRealDomInfo = function (nexacroObj) {
  if (!nexacroObj) nexacroObj = this;
  const id = this.getDomId(nexacroObj);
  const domObj = document.getElementById(id);

  return {
    nexacroObj: nexacroObj,
    element: domObj,
    position: {
      left: nexacroObj.getOffsetLeft(),
      top: nexacroObj.getOffsetTop(),
      right: nexacroObj.getOffsetRight(),
      bottom: nexacroObj.getOffsetBottom(),
      width: nexacroObj.getOffsetWidth(),
      height: nexacroObj.getOffsetHeight(),
    },
  };
};
```

`callVueElement`

##### vue component 생성 및 vue mount

```js
this.callVueElement = function () {
  trace("callback start for mounting vue");
  // Create a Vue application
  const app = Vue.createApp({});

  // Define a new global component called button-counter
  app.component("button-counter", {
    data() {
      return {
        count: 0,
      };
    },
    template: `
		<button class="btn" v-on:click="count++">
		  I am Vue component.<br>
		  You clicked me {{ count }} times.
		</button>`,
  });

  app.mount("#app");
};
```

`addCSS`

##### vue component 를 위한 style 추가

```js
this.addCSS = function (css) {
  const styleSheet = document.styleSheets[document.styleSheets.length - 1];
  styleSheet.insertRule(css);
};
```

`Button00_onclick`

##### vue 의 count 예제와 같은 count 를 위한 스크립트

```js
this.Button00_onclick = function (obj: nexacro.Button, e: nexacro.ClickEventInfo) {
  const ds = this["ds" + obj.id];
  const cnt = ds.getColumn(0, 0) + 1;
  const text = `I am Nexacro component.\nYou clicked me ${cnt} times.`;

  obj.set_text(text);
  ds.setColumn(0, 0, cnt);
};
```

> #### Form 에서 파란색 버튼은 Form 에 버튼을 직접 그려 처리한 예

> #### Form 에서 빨간색 버튼은 div 를 이용하여 처리한 예
