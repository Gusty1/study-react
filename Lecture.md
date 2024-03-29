# React_Basic

## React 的基礎知識

---

# axios

## 學習 axios

---

# React_staging

## React 的開發工具和應用範例

首先把這專案用 yarn 或 npm 開啟，然後要看練習項目把當前的 src 改名，把要看的資料夾改成 src 就好  
原本第 4 章是講甚麼代理配置，但那很需要 nodeJs 和一堆神奇的東西，總之就是聽不懂~~~  
github 搜尋案例原本還有個 fetch 篇，但我就聽過而已，沒有記錄下來

## 一、todoList 案例相關知識

1. 拆分組件、實現靜態組件，注意:className、style 的寫法
2. 動態初始化列表，如何確定將數據放在哪個組件的 state 中?
   1. 某個組件使用:放在其自身的 state 中
   2. 某些組件使用:放在他們共同的父組件 state 中(官方稱此操作為:狀態提升)
3. 關於父子之間通信:
   1. 「父組件」給「子組件」傳遞數據:通過 props 傳遞
   2. 「子組件」給「父祖件」傳遞數據:要求父提前給子傳遞一個函數
4. 注意 defaultChecked 和 checked 的區別，類似的還有 defaultValue 和 value
5. 狀態在哪裡，操作的方法就在哪裡

## 二、github 搜尋案例相關知識

1. 設計狀態時要考慮全面，例如帶有網路請求的組件，要考慮請求失敗怎麼辦
2. ES6 小知識:解構賦值+重命名

```js
let obj ={a:{b:1}}
const {a} = obj //傳統解構賦值
const {a:{b}} =obj //連續解構賦值
const {a:{b:value}} //連續解構賦值+重命名
```

3. 消息訂閱與發佈機制
   1. 先訂閱，再發佈
   2. 適用於任意組件通信
   3. 要在組件的 componentWillUnmount 中取消訂閱

## 三、路由的基本使用

1. 明確好介面中的導航區、展示區
2. 導航區中的 a 標籤改為 Link 標籤

```js
<Link to="/xxxxx">Demo</Link>
```

3. 展示區寫 Route 標籤進行路徑的匹配

```js
<Route path="/xxxxx" components={Demo} />
```

4.  &lt;App/&gt;的最外側包裹了一個&lt;BrowserRouter&gt;或&lt;HashRouter&gt;

## 四、路由組件與一般組件

1. 寫法不同:
   - 一般組件:&lt;Demo/&gt;
   - 路由組件:&lt;Route path="/demo" components={Demo}/&gt;
2. 存放位置不同:
   - 一般組件:components
   - 路由組件:pages
3. 接收到的 prop 不同:
   - 一般組件:組件標籤傳遞甚麼，就收到甚麼
   - 路由組件:接收到 3 個固定的屬性

```js
history:
  go: function(n)
  goBack: function()
  goForward: function()
  push: function(path,state)
  replace: function(path,state)
location:
  pathname: ""
  search: ""
  state: {}
match:
   params: {}
   path: ""
   url: ""
```

## 五、NavLink 與封裝 NavLink

1. NavLink 可以實現路由鏈接的高亮，通過 activeClassName 指定樣式名(預設是 active)
2. 標籤體內容是一個特殊的標籤屬性
3. 通過 this.props.children 可以獲取標籤體內容

## 六、Switch 的使用

1. 通常情況下，path 和 component 是一種對應的關係
2. Switch 可以提高路由匹配效率(單一匹配)

## 七、解決多級路徑刷新頁面樣式丟失問題

1. public/index.html 中 引入樣式時不寫 ./ 改寫 / (常用)
2. public/index.html 中 引入樣式時不寫 ./ 改寫 %PUBLIC_URL% (常用)
3. 使用 HashRouter

## 八、解決多級路徑刷新頁面樣式丟失問題

1. 默認使用的是模糊匹配(簡單記:「輸入的路徑」必須包含要「匹配的路徑」，且順序要一致)
2. 開啟嚴格匹配: &lt;Route exact path="/demo" component={Demo} /&gt;
3. 嚴格匹配不要隨便開啟，需要再開，有時候開啟會導致無法繼續匹配二級路由

## 九、Redirect 的使用

1. 一般寫在所有路由註冊的最下方，當所有路由都無法匹配時，跳轉到 Redirect 指定的路由
2. 具體編碼:

```js
<Switch>
    
  <Route path="/about" component={About} />
    
  <Route path="/home" component={Home} />
    
  <Redirect to="/about" />
</Switch>
```

## 十、嵌套路由

1. 註冊子路由時要寫上父路由的 path 值
2. 路由的匹配是按照註冊路由的順序進行

## 十一、向路由組件傳遞參數

1. params 參數:
   - 路由鏈接(攜帶參數): &lt;Link to="/demo/test/tom/18"&gt;詳情&lt;Link&gt;
   - 註冊路由(聲明接收): &lt;Route path="/demo/test/:name/:age" component={Test}/&gt;
   - 接收參數: this.props.match.params
2. search 參數:
   - 路由鏈接(攜帶參數): &lt;Link to="/demo/test?name=tom&age=18"&gt;詳情&lt;/Link&gt;
   - 註冊路由(無須聲明，正常註冊即可):&lt;Route path="/demo/test" component={Test}/&gt;
   - 接收參數: this.props.location.search
   - 備註: 獲取到的 search 是 urlencoded 編碼字符串，需要藉助 query-string 的解析
3. state 參數:
   - 路由鏈接(攜帶參數): &lt;Link to={{pathname:'/demo/test',state:{name:'tom',age:18}}}&gt;詳情&lt;/Link&gt;
   - 註冊路由(無須聲明，正常註冊即可): &lt;Route path="/demo/test" component={Test}/&gt;
   - 接收參數: this.props.location.state
   - 備註: 刷新也可以保留住參數

## 十二、編程式路由導航

借助 this.props.history 對象上的 API 對操作路由跳轉、前進、後退

```js
this.props.history.push()
this.props.history.replace()
this.props.history.goBack()
this.props.history.goForward()
this.props.history.go()
```

## 十三、BrowserRouter 與 HashRouter 的區別

1. 底層原理不一樣:
   - BrowserRouter 使用的是 H5 的 history API，不兼容 IE9 級以下的版本
   - HashRouter 使用的是 URL 的哈希值
2. path 表現形式不一樣:
   - BrowserRouter 的路徑中沒有#，例如: localhost:3000/demo/test
   - HashRouter 的路徑包含#，例如: localhost:3000/#/demo/test
3. 刷新後對路由 state 參數的影響:
   - BrowserRouter 沒有任何影響，因為 state 會保存在 history 的對象中
   - HashRouter 刷新後會導致路由 state 的丟失
4. 備註:HashRouter 可以用於解決一些路徑錯誤相關的問題

額外安裝的庫:

1. nanoid: 用於產生 uuid；import {nanoid} form 'nanoid'；使用:nanoid()
2. prop-types: 限制 prop 的類型；import PropTypes from 'prop-types'
3. axios: ajax 的東西；import axios from 'axios'
4. pubsub-js: 訂閱和消息發布； import PubSub from 'pubsub-js'
5. react-router-dom: web 用的路由組件；import { NavLink, Route,BrowserRouter,Switch,Redirect} from 'react-router-dom'

備註:

1.  uuid 可以裝 uuid，但影片說 uuid 偏大，所以推薦 nanoid
2.  import 潛規則:第 3 方的都往上，自己寫得靠下，css 放最後
3.  已經寫好的 css 在 public 創建一個 css 資料夾，在 index.html 引入
4.  注意 public 的 index.html 有引入 bootstrap，要看其他項目的時候注意一下
5.  query-string 用於解析網址參數，腳手架已經安裝好了，import qs from 'query-string'

---

# Redux

## 1.求和案例\_redux 精簡版

1. 去除 Count 組件自身的狀態
2. src 下建立:

```js
;-src - redux - store.js - count_reducer.js
```

3. store.js:
   1. 引入 redux 中的 createStore 函數，創建一個 store
   2. createStore 調用時要傳入一個為其服務的 reducer
   3. 記得暴露 store 對象
4. store.js:
   1. reducer 的本質是一個函數，接收: preState、action，返回加工後的狀態
   2. reducer 有兩個作用:初始化狀態，加工狀態
   3. reducer 被第一次調用時，是 store 自動觸發的
      - 傳遞的 preState: undefined
      - 傳遞的 action: {type:'@@REDUX/INIT_a.2.b.4'}
5. 在 index.js 中監測 store 中狀態的改變，一旦發生改變重新渲染&lt;App/&gt;
   - 備註:redux 只負責狀態管理，至於狀態的改變驅動著頁面的展示，要靠我們自己寫

## 2.求和案例\_redux 完整版

新增文件:

1. count_action.js 專門用於創建 action 對象
2. constant.js 放置容易寫錯的 type 值

## 3.求和案例\_redux 異步 action 版

1. 明確: 延遲的動作不想要交給組件自身，想交給 action
2. 何時需要異步 action: 想要對狀態進行操作，但是具體的數據靠異步任務返回
3. 具體編碼:
   1. npm install redux-thunk，並配置在 store 中
   2. 創建 action 的函數不再返回一般對象，而是一個函數，該函數中寫異步任務
   3. 異步任務有結果後，分發一個同步的 action 去真正操作數據
4. 備註: 異步 action 不是必須要寫的，完全可以自己等待異步任務的結果了再去發同步 action

## 4.求和案例\_react-redux 基本使用

1. 明確 2 個概念:
   1. UI 組件: 不能使用任何 redux 的 api，只負責頁面的呈現、交互等
   2. 容器組件: 負責和 redux 通信，將結果交給 UI 組件
2. 如何創建一個組件容器-->靠 react-redux 的 connect 函數
   - connect(mapStateToProps, mapDispatchToProps)(UI 組件)
     - mapStateToProps:映射狀態，返回值是一個對象
     - mapDispatchToProps:映射操作狀態的方法，返回值是一個對象

- 備註 1: 容器組件的 store 是靠 props 傳進去的，而不是在容器組中直接引入
- 備註 2: mapDispatchToProps 也可以是一個對象{}

## 5.求和案例\_react-redux 優化

1. 容器組件和 UI 組件混成一個文件
2. 無須自己給容器組件傳遞 store，給&lt;App/&gt;包裹一個&lt;Provider store={store}&gt;即可
3. 使用 react-redux 後不用再自己監測 redux 中狀態的改變了，容器組件可以自動完成這個工作
4. mapDispatchToProps 也可以簡單地寫成一個對象
5. 一個組件要和 redux 打交道要經過哪幾步
   1. 定義好 UI 組件(不暴露)
   2. 引入 connect 生成一個容器組件，並暴露，寫法如下
   3. 在 UI 組件中通過 this.props.xxx 讀取和操作狀態

```js
connect(
  (state) => ({ key: value }), //映射狀態
  { key: xxxAction } //映射狀態操作方法
)(UI組件)
```

## 6.求和案例\_react-redux 數據共享版

1. 定義一個 Person 組件，和 Count 組件通過 redux 共享數據
2. 為 Person 組件編寫: redux、action，配置 constant'常量
3. 重點: Person 的 reducer 和 Count 的 Reducer 要使用 combineReducers 進行合併，合併的狀態是一個對象
4. 交給 store 的是總 reducer，最後注意在組件中取出狀態的時候記得「取到位」

## 7.求和案例\_react-redux 開發者工具的使用

1. npm add redux-devtools-extension
2. store 商店進行配置

```js
import { composeWithDevTools } from 'redux-devtools-extension'
export default createStore(
  allReducer,
  composeWithDevTools(applyMiddleware(thunk))
)
```

## 8.求和案例\_react-redux 最終版

1. 所有變量名字要規範，盡量觸發對象的簡寫形式
2. reducers 文件夾中，編寫 index.js 專門用於匯總並暴露所有的 reducer

---

# React_extension

## React 的擴展知識

## 1.setState 更新狀態的 2 種方法

1. setState(stateChange,[callback])---對象式的 setState
   1. stateChange 為狀態改變對象(該對象可以體現出狀態的更改)
   2. callback 是可選的回調函數，他在狀態、頁面更新完畢後(render 調用後)才被調用
2. setState(updater,[callback])---函數式的 setState  
    1. updater 為返回 stateChange 對象的函數  
    2. updater 可以接收到 state 和 props  
    3. callback 是可選的回調函數，他在狀態、頁面更新完畢後(render 調用後)才被調用  
   總結:
3. 對象式的 setState 是函數式的 setState 簡寫方式
4. 使用原則:
   1. 如果更新狀態不依賴於原狀態==&gt;使用對象方式
   2. 如果更新狀態依賴於原狀態==&gt;使用函數方式
   3. 如果需要在 setState()執行後獲取最新狀態的數據，要在第 2 個 callback 函數中獲取

## 2.路由組件的 lazyLoad

1. 通過 React 的 lazy 函數配合 import()函數動態加載路由組件==>路由組件代碼會被分開打包

```js
const Login = lazy(() => import('/pages/...'))
```

2. 通過&lt;Suspense&gt;指定在加載得到路由打包文件前顯示一個自定義 Loading 介面

```js
<Suspense fallback={Loading}>
    
  <Route path="/about" element={About} />
    
  <Route path="/home" element={Home} />
</Suspense>
```

## 3.Hooks

1. Hook/Hooks 是甚麼?
   1. Hook 是 React 16.8.0 版新增的特性/語法
   2. 可以讓你在函數中使用 state 以及其他的 React 特性
2. 三個常用的 Hook
   1. State Hook: React.useState()
   2. Effect Hook: React.useEffect()
   3. Ref Hook: React.useRef()
3. State Hook
   1. State Hook 讓函數也可以有 state 狀態，並進行狀態數據的讀寫操作
   2. 語法: const [xxx,setXxx] = React.useState(initValue)
   3. useState()說明:
      - 參數: 第一次初始化指定的值在內部做緩存
      - 返回值: 包含 2 個元素的數組，第一個為內部當前狀態值，第 2 個為更新狀態值的函數
   4. setXxx()2 種寫法:
      - setXxx(newValue): 參數為非函數值，直接指定新的狀態值，內部用其覆蓋原來的狀態值
      - setXxx(value=> newValue): 參數為函數，接收原本的狀態值，返回新的狀態值，內部用其覆蓋原來的狀態值
4. EffectHook
   1. Effect Hook 可以讓你在函數組件中執行副作用操作(用於模擬類組件中的生命週期鉤子)
   2. React 中的副作用操作:
      - 發 ajax 請求
      - 設置訂閱/啟動定時器
      - 手動更改真實 DOM
   3. 可以把 useEffect Hook 看做如下 3 個函數的組合:
      - componentDidMount()
      - componentDidUpdate()
      - componentWillUnmount()
   4. 語法和說明:

```js
useEffect(() => {
  //在此可以執行任何副作用操作
  return () => {
    //在組件卸載前執行
    //在此做一些收尾工作，例取消定時器、取消訂閱...
  }
}, [stateValue]) //如果指定的是[]，回調函數只會在第一次render()後執行
```

5. RefHook
   1. Ref Hook 可以在函數組件中存储/查找組件內的標籤或任意其他的數據
   2. 語法: const refContainer = React.useRef()
   3. 作用: 保存標籤對象，功能與 React.createRef()一致

## 4.Fragment

1. 使用:

```js
<Fragment>
  <></>
</Fragment>
```

2. 作用: 可以不用必須有一個真實的 DOM 根標籤

## 5.Context

1. 理解: 一種組件間的通信方式，常用於「祖祖件」與「後代組件」間通信
2. 使用:
   1. 創建 Context 容器對象
      - const XxxContext = React.createContext()
   2. 渲染子組件時，外面包裹 xxxContext.Provider，通過 value 屬性給後代祖件傳遞數據:

```js
<xxxContext.Provider value={數據}>  子組件</xxxContext.Provider>
```

3.  後代祖件讀取數據:
    - 第一種方式:僅適用於類組件
    - 第二種方式:函數、類組件都可以

```js
//第一種方式
static contextType = xxxContext//聲明接收context
this.context//讀取context的value數據
```

```js
//第二種方式
<xxxContext.Consumer>
  {(
    value //value就是context中的value數據
  ) => 要顯示的內容}
</xxxContext.Consumer>
```

3. 注意: 在應用開發中一般不用 context，都用它封裝的 react 插件

## 6.組件優化

1. component 的 2 個問題:
   1. 只要執行 setState()即使不改變狀態數據，組件也會重新 render
   2. 當前組件重新 render()就會自動重新 render 子組件，縱使子組件沒有用到任何父祖件的數據==&gt;效率低
2. 解決方法:
   1. 方法 1: 重寫 shouldComponentUpdate()方法，比較新舊 state 或 props 數據，如果有變化才返回 true，沒有返回 false
   2. 方法 2: 使用 PureComponent，PureComponent 重寫了 shouldComponentUpdate()，只有 state 或 props 數據有變化才返回 true
      - **只是進行 state 和 props 數據的比較，如果只是數據對象內部數據改變了，返回 false**
      - **不要直接修改 state 數據，?是要產生新數據**
3. 項目中一般使用 PureComponent 來優化

## 7.render props

1. 如何向組件內部動態傳入帶內容的結構(標籤)?
   1. 使用 children props:通過組件標籤體傳入結構
   2. 使用 render props:通過組件標籤屬性傳入結構，而且可以攜帶數據，一般用 render 函數屬性
2. children props:

```js
<A>
    <B>xxx</B>
</A>
```

- {this.props.children}
- B 組件不能得到 A 組件的數據

3. render props:
   - A 組件: {this.props.render(內部 state 數據)}
   - C 組件: 讀取 A 組件傳入的數據顯示{this.props.data}

```js
<A
  render={(data) => {
    ;<C data={data}></C>
  }}></A>
```

## 8.錯誤邊界

1. 理解: 錯誤邊界(Error boundary): 用來捕獲後代組件錯誤、渲染出備用頁面
2. 特點: 只能捕獲後代組件生命週期發生的錯誤，不能捕獲自己組件產生的錯誤和其他組件在合成事件、定時器中產生的錯誤
3. 使用方式: getDerivedStateFromError 配合 componentDidCatch

```js
static getDerivedStateFromError(error){
    console.log(error)
    //再render之前返回新的state
    return {
        hasError:true
    }
}
    componentDidCatch(error,info){
    //統計頁面錯誤，發送請求到後端
    console.log(error,info)
}
```

## 9.組件通信方式總節

1. 組件間的關係:
   - 父子組件
   - 兄弟組件(非嵌套組件)
   - 祖孫組件(跨級組件)
2. 幾種通信方式:
   - props:
     - children props
     - render props
   - 消息訂閱-發佈:
     - pubs-sub、event...
   - 集中式管理:
     - redux、dva...
   - conText:
     - 生產者-消費者模式
3. 比較好的搭配方式:
   - 父子組件: props
   - 兄弟組件: 消息訂閱-發佈、集中式管理
   - 祖孫組件: 消息訂閱-發布、集中式管理、conText(用的較少)

---

# React_router6

## react-router-dom6 版的一些新東西

> > > > > > > parent of 9c7ccfd (restudy)
