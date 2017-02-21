---
layout: post
title: React Native系列 - props vs propTypes

categories:
- React Native
- 技术

tags:
- props
- JavaScript
- propTypes

excerpt: 在写react js代码的时候经常看到有类型声明语句props和propTypes两种写法，这两者写法分别有啥作用，又有啥区别？
---

# props 和propTypes
在reactjs里面经常看到两种成员变量类型声明语句，props和propTypes.

propTypes pure function component写法:

~~~JavaScript
const ListOfNumbers = props => (
  <ol className={props.className}>
    {
      props.numbers.map(number => (
        <li>{number}</li>)
      )
    }
  </ol>
);

ListOfNumbers.propTypes = {
  className: React.PropTypes.string.isRequired,
  numbers: React.PropTypes.arrayOf(React.PropTypes.number)
};
~~~

propTypes ES6 写法(摘自web.android.js, react-native 0.38):

~~~JavaScript
class WebView extends React.Component {
static propTypes = {
    ...View.propTypes,
    renderError: PropTypes.func,
    renderLoading: PropTypes.func,
    onLoad: PropTypes.func,
    onLoadEnd: PropTypes.func,
    onLoadStart: PropTypes.func,
    onError: PropTypes.func,
    automaticallyAdjustContentInsets: PropTypes.bool,
    contentInset: EdgeInsetsPropType,
    onNavigationStateChange: PropTypes.func,
    onMessage: PropTypes.func,
    onUrlBlocked: PropTypes.func,
    onUrlIntercepted: PropTypes.func,
    onContentSizeChange: PropTypes.func,
    startInLoadingState: PropTypes.bool, // force WebView to show loadingView on first load
    style: View.propTypes.style,

    blockList: PropTypes.array,
    interceptList: PropTypes.array,
    whiteUrlStrings: PropTypes.array,

    html: deprecatedPropType(
      PropTypes.string,
      'Use the `source` prop instead.'
    ),

    url: deprecatedPropType(
      PropTypes.string,
      'Use the `source` prop instead.'
    ),

    /**
     * Loads static html or a uri (with optional headers) in the WebView.
     */
    source: PropTypes.oneOfType([
      PropTypes.shape({
        /*
         * The URI to load in the WebView. Can be a local or remote file.
         */
        uri: PropTypes.string,
        /*
         * The HTTP Method to use. Defaults to GET if not specified.
         * NOTE: On Android, only GET and POST are supported.
         */
        method: PropTypes.oneOf(['GET', 'POST']),
        /*
         * Additional HTTP headers to send with the request.
         * NOTE: On Android, this can only be used with GET requests.
         */
        headers: PropTypes.object,
        /*
         * The HTTP body to send with the request. This must be a valid
         * UTF-8 string, and will be sent exactly as specified, with no
         * additional encoding (e.g. URL-escaping or base64) applied.
         * NOTE: On Android, this can only be used with POST requests.
         */
        body: PropTypes.string,
      }),
      PropTypes.shape({
        /*
         * A static HTML page to display in the WebView.
         */
        html: PropTypes.string,
        /*
         * The base URL to be used for any relative links in the HTML.
         */
        baseUrl: PropTypes.string,
      }),
      /*
       * Used internally by packager.
       */
      PropTypes.number,
    ]),

    /**
     * Used on Android only, JS is enabled by default for WebView on iOS
     * @platform android
     */
    javaScriptEnabled: PropTypes.bool,

    /**
     * Used on Android only, controls whether DOM Storage is enabled or not
     * @platform android
     */
    domStorageEnabled: PropTypes.bool,

    /**
     * Sets the JS to be injected when the webpage loads.
     */
    injectedJavaScript: PropTypes.string,

    /**
     * Sets whether the webpage scales to fit the view and the user can change the scale.
     */
    scalesPageToFit: PropTypes.bool,

    /**
     * Sets the user-agent for this WebView. The user-agent can also be set in native using
     * WebViewConfig. This prop will overwrite that config.
     */
    userAgent: PropTypes.string,

    /**
     * Used to locate this view in end-to-end tests.
     */
    testID: PropTypes.string,

    /**
     * Determines whether HTML5 audio & videos require the user to tap before they can
     * start playing. The default value is `false`.
     */
    mediaPlaybackRequiresUserAction: PropTypes.bool,
  };

  static defaultProps = {
    javaScriptEnabled : true,
    scalesPageToFit: true,
  };

  state = {
    viewState: WebViewState.IDLE,
    lastErrorEvent: null,
    startInLoadingState: true,
  };

  render() {
    ...
  }

}
~~~

# props
较常见的props写法

~~~JavaScript
class Counter extends Component {
  props: {
    increment: () => void,
    incrementIfOdd: () => void,
    incrementAsync: () => void,
    decrement: () => void,
    counter: number
  };
  render(){}
}

没有找到比较正式的文档解释这语法的出处和用法。

~~~

# propTypes

react js 会在develop模式下开启针对propType的检测，运行的时候如果某个组件调用传的props不符合要求就会抛一个红色错误。Release下不会做type check.

在

一个[小提示](https://camjackson.net/post/9-things-every-reactjs-beginner-should-know)，可以在unit test里面做静态语法检查，让所有的propType错误从unit test里面抛出来

~~~JavaScript
beforeAll(() => {
  console.error = error => {
    throw new Error(error);
  };
});
~~~


# TypeScript


# 参考
[propTypes背后是一系列检测函数](http://www.ian-thomas.net/custom-proptype-validation-with-react/), 教你怎样写自己的类型检测函数。
