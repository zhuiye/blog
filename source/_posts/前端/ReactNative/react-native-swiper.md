---
title: react-native-swiper
date: 2019-11-18 21:48:14
tags: React Native
---
https://github.com/leecade/react-native-swiper

今天学习了一下 react-native-swiper 库的源码,代码行数中除去声明属性，和设置默认值，代码函数不多，600行上下。
轮播原理的实现：ScrollView为基础容器，设置定时器指定时间内执行 ScrollView.scrollTo()即可
接下来，我就直接贴代码，然后注释就在里面了,以下分析的流程是官方example的流程

大致执行流程如下：
```js
    // 初始化
    state = this.initState(this.props)
    render()构建基本的视图,scrollView 以及子视图 的dom对象构建
    componentDidMount() 执行
    View 的 onLayout()回调执行，手动触发scrollView.scrollTo 方法，定位到正确位置

    点击next 按钮，执行如下
    scrollBy(1)  调用 ，执行滚动，更新状态

    onScrollEnd()执行
    updateIndex()
  
```

源码如下:
```js
/**
 * react-native-swiper
 * @author leecade<leecade@163.com>
 */
import React, { Component } from 'react'
import PropTypes from 'prop-types'
import {
  Text,
  View,
  ViewPropTypes,
  ScrollView,
  Dimensions,
  TouchableOpacity,
  Platform,
  ActivityIndicator
} from 'react-native'

/**
 * Default styles  // 默认样式...
 * @type {StyleSheetPropType}
 */
const styles = {
  container: {
    backgroundColor: 'transparent',
    position: 'relative',
    flex: 1
  },

  wrapperIOS: {
    backgroundColor: 'transparent'
  },

  wrapperAndroid: {
    backgroundColor: 'transparent',
    flex: 1
  },

  slide: {
    backgroundColor: 'transparent'
  },

  pagination_x: {
    position: 'absolute',
    bottom: 25,
    left: 0,
    right: 0,
    flexDirection: 'row',
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: 'transparent'
  },

  pagination_y: {
    position: 'absolute',
    right: 15,
    top: 0,
    bottom: 0,
    flexDirection: 'column',
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: 'transparent'
  },

  title: {
    height: 30,
    justifyContent: 'center',
    position: 'absolute',
    paddingLeft: 10,
    bottom: -30,
    left: 0,
    flexWrap: 'nowrap',
    width: 250,
    backgroundColor: 'transparent'
  },

  buttonWrapper: {
    backgroundColor: 'transparent',
    flexDirection: 'row',
    position: 'absolute',
    top: 0,
    left: 0,
    flex: 1,
    paddingHorizontal: 10,
    paddingVertical: 10,
    justifyContent: 'space-between',
    alignItems: 'center'
  },

  buttonText: {
    fontSize: 50,
    color: '#007aff'
  }
}

// missing `module.exports = exports['default'];` with babel6
// export default React.createClass({
export default class extends Component {
  /**
   * Props Validation // 属性声明 proTypes ,代码很老了，现在大部分重构都用typescript
   * @type {Object}
   */
  static propTypes = {
    horizontal: PropTypes.bool,
    children: PropTypes.node.isRequired,
    containerStyle: PropTypes.oneOfType([PropTypes.object, PropTypes.number]),
    style: PropTypes.oneOfType([
      PropTypes.object,
      PropTypes.number,
      PropTypes.array
    ]),
    scrollViewStyle: PropTypes.oneOfType([PropTypes.object, PropTypes.number]),
    pagingEnabled: PropTypes.bool,
    showsHorizontalScrollIndicator: PropTypes.bool,
    showsVerticalScrollIndicator: PropTypes.bool,
    bounces: PropTypes.bool,
    scrollsToTop: PropTypes.bool,
    removeClippedSubviews: PropTypes.bool,
    automaticallyAdjustContentInsets: PropTypes.bool,
    showsPagination: PropTypes.bool,
    showsButtons: PropTypes.bool,
    disableNextButton: PropTypes.bool,
    disablePrevButton: PropTypes.bool,
    loadMinimal: PropTypes.bool,
    loadMinimalSize: PropTypes.number,
    loadMinimalLoader: PropTypes.element,
    loop: PropTypes.bool,
    autoplay: PropTypes.bool,
    autoplayTimeout: PropTypes.number,
    autoplayDirection: PropTypes.bool,
    index: PropTypes.number,
    renderPagination: PropTypes.func,
    dotStyle: PropTypes.oneOfType([
      PropTypes.object,
      PropTypes.number,
      PropTypes.array
    ]),
    activeDotStyle: PropTypes.oneOfType([
      PropTypes.object,
      PropTypes.number,
      PropTypes.array
    ]),
    dotColor: PropTypes.string,
    activeDotColor: PropTypes.string,
    /**
     * Called when the index has changed because the user swiped.
     */
    onIndexChanged: PropTypes.func
  }

  /**
   * Default props
   * @return {object} props 
   * @see http://facebook.github.io/react-native/docs/scrollview.html
   * @see https://reactnative.cn/docs/scrollview/
   */
  static defaultProps = {
    horizontal: true, // 默认水平方向
    pagingEnabled: true, // scrollview 中的属性 当值为true时，滚动条会停在滚动视图的尺寸的整数倍位置。这个可以用在水平分页上。默认值为false。
    showsHorizontalScrollIndicator: false, // 默认不展示水平指示器
    showsVerticalScrollIndicator: false, // 默认不展示垂直指示器
    bounces: false, //默认不弹性
    scrollsToTop: false, // 默认点击状态栏不返回ScrollView 顶部
    removeClippedSubviews: true,
    automaticallyAdjustContentInsets: false,
    showsPagination: true,  // 自定义属性:是否展示指示器
    showsButtons: false, // 是否展示按钮
    disableNextButton: false, // 是否禁用next 按钮
    disablePrevButton: false, // 是否禁用pre按钮
    loop: true, // 是否循环? 默认true
    loadMinimal: false,
    loadMinimalSize: 1,
    autoplay: false, // 默认不自动播放
    autoplayTimeout: 2.5, // 播放间隔2.5秒
    autoplayDirection: true, // 播放方向 next...
    index: 0, // 当前 索引
    onIndexChanged: () => null
  }

  /**
   * Init states  //初始化 state
   * @return {object} states
   */
  state = this.initState(this.props)

  /**
   * Initial render flag
   * @type {bool}
   */
  initialRender = true

  /**
   * autoplay timer
   * @type {null}
   */
  autoplayTimer = null
  loopJumpTimer = null

  componentWillReceiveProps(nextProps) {
    if (!nextProps.autoplay && this.autoplayTimer)
      clearTimeout(this.autoplayTimer)
    if (nextProps.index === this.props.index) return
    this.setState(
      this.initState(nextProps, this.props.index !== nextProps.index)
    )
  }

  componentDidMount() {
    this.autoplay()
  }

  componentWillUnmount() {
    this.autoplayTimer && clearTimeout(this.autoplayTimer)
    this.loopJumpTimer && clearTimeout(this.loopJumpTimer)
  }

  componentWillUpdate(nextProps, nextState) {
    // If the index has changed, we notify the parent via the onIndexChanged callback
    if (this.state.index !== nextState.index)
      this.props.onIndexChanged(nextState.index)
  }

  componentDidUpdate(prevProps) {
    // If autoplay props updated to true, autoplay immediately
    if (this.props.autoplay && !prevProps.autoplay) {
      this.autoplay()
    }
    if (this.props.children !== prevProps.children) {
      this.setState(
        this.initState({ ...this.props, index: this.state.index }, true)
      )
    }
  }

  initState(props, updateIndex = false) {
    // set the current state
    // 设置state ,初始,this.state =null
    const state = this.state || { width: 0, height: 0, offset: { x: 0, y: 0 } }

    const initState = {
      autoplayEnd: false, //是否播放到末尾
      children: null, // 子元素
      loopJump: false, // 这个状态暂时未知???
      offset: {} // 偏移量
    }

    // Support Optional render page
    initState.children = Array.isArray(props.children)
      ? props.children.filter(child => child)
      : props.children

    // 获取 共有多少个 子元素 ,轮播项
    initState.total = initState.children ? initState.children.length || 1 : 0
    
    // 初始化，并不执行这部
    if (state.total === initState.total && !updateIndex) {
      // retain the index
      initState.index = state.index
    } else {
        //  通过己算,索引为第一项 index=0
      initState.index =
        initState.total > 1 ? Math.min(props.index, initState.total - 1) : 0
    }

    // Default: horizontal
    const { width, height } = Dimensions.get('window')
    
    // 默认dir =x;
    initState.dir = props.horizontal === false ? 'y' : 'x'
     // 设置 每一项的 高度的宽度
    if (props.width) {
      initState.width = props.width
    } else if (this.state && this.state.width) {
      initState.width = this.state.width
    } else {
      initState.width = width
    }

    if (props.height) {
      initState.height = props.height
    } else if (this.state && this.state.height) {
      initState.height = this.state.height
    } else {
      initState.height = height
    }

    // 默认为屏幕的宽度和高度
    // 初始的偏移量为 {x:0}
    initState.offset[initState.dir] =
      initState.dir === 'y' ? height * props.index : width * props.index
    // 设置内部的状态 isScrolling:false
    this.internals = {
      ...this.internals,
      isScrolling: false
    }
    return initState
  }

  // include internals with state
  fullState() {
      // 该组件中全部的状态
    return Object.assign({}, this.state, this.internals)
  }

  onLayout = event => {
      // view 动态布局计算完毕后执行
    const { width, height } = event.nativeEvent.layout
    // 设置内容的offset 
    const offset = (this.internals.offset = {})
    const state = { width, height }

    if (this.state.total > 1) {
        // 设置当前的 index
      let setup = this.state.index
      if (this.props.loop) {
        setup++
      }
      // 计算偏移量
      offset[this.state.dir] =
        this.state.dir === 'y' ? height * setup : width * setup
      // {x:360}
    }

    // only update the offset in state if needed, updating offset while swiping
    // causes some bad jumping / stuttering
    if ( !this.state.offset ||
      width !== this.state.width ||
      height !== this.state.height
    ) {
      state.offset = offset
    }
    // contentOffset 仅为iOS属性
    // related to https://github.com/leecade/react-native-swiper/issues/570
    // contentOffset is not working in react 0.48.x so we need to use scrollTo
    // to emulate offset.
    if (this.initialRender && this.state.total > 1) {
        // 手动转到正确的位置 偏移{x:360} 因为第page数组的第一项为：“最后一个” 
      this.scrollView.scrollTo({ ...offset, animated: false })
      this.initialRender = false
    }
   // 更新state 
    this.setState(state)
  }

  loopJump = () => {
    if (!this.state.loopJump) return
    const i = this.state.index + (this.props.loop ? 1 : 0)
    const scrollView = this.scrollView
    this.loopJumpTimer = setTimeout(
      () =>
        scrollView.setPageWithoutAnimation &&
        scrollView.setPageWithoutAnimation(i),
      50
    )
  }

  /**
   * Automatic rolling  自动播放的逻辑
   */
  autoplay = () => {
    if (
      !Array.isArray(this.state.children) ||
      !this.props.autoplay ||
      this.internals.isScrolling ||
      this.state.autoplayEnd
    )
      return

    this.autoplayTimer && clearTimeout(this.autoplayTimer)
    this.autoplayTimer = setTimeout(() => {
        // 如果只有一个，
      if (
        !this.props.loop &&
        (this.props.autoplayDirection
          ? this.state.index === this.state.total - 1
          : this.state.index === 0)
      )
        return this.setState({ autoplayEnd: true })

      this.scrollBy(this.props.autoplayDirection ? 1 : -1)
    }, this.props.autoplayTimeout * 1000)
  }

  /**
   * Scroll begin handle
   * @param  {object} e native event //开始滚动执行 
   */
  onScrollBegin = e => {
    // update scroll state
    this.internals.isScrolling = true
    this.props.onScrollBeginDrag &&
      this.props.onScrollBeginDrag(e, this.fullState(), this)
  }

  /**
   * Scroll end handle
   * @param  {object} e native event 滚动结束
   */
  onScrollEnd = e => {
    // update scroll state 滚动结束
    this.internals.isScrolling = false

    // making our events coming from android compatible to updateIndex logic
    // 手动调用 走里面流程 
    if (!e.nativeEvent.contentOffset) {
      if (this.state.dir === 'x') {
        e.nativeEvent.contentOffset = {
          x: e.nativeEvent.position * this.state.width  // 2*360=720
        }
      } else {
        e.nativeEvent.contentOffset = {
          y: e.nativeEvent.position * this.state.height
        }
      }
    }

    this.updateIndex(e.nativeEvent.contentOffset, this.state.dir, () => {
        // 更新完状态之后，执行autoplay()
      this.autoplay()
      this.loopJump()

      // if `onMomentumScrollEnd` registered will be called here
      this.props.onMomentumScrollEnd &&
        this.props.onMomentumScrollEnd(e, this.fullState(), this)
    })
  }

  /*
   * Drag end handle
   * @param {object} e native event  //这个是只有在拖动下才触发
   */
  onScrollEndDrag = e => {
    const { contentOffset } = e.nativeEvent
    const { horizontal } = this.props
    const { children, index } = this.state
    const { offset } = this.internals
    const previousOffset = horizontal ? offset.x : offset.y
    const newOffset = horizontal ? contentOffset.x : contentOffset.y

    if (
      previousOffset === newOffset &&
      (index === 0 || index === children.length - 1)
    ) {
      this.internals.isScrolling = false
    }
  }

  /**
   * Update index after scroll
   * @param  {object} offset content offset
   * @param  {string} dir    'x' || 'y'
   */
  updateIndex = (offset, dir, cb) => {
    const state = this.state
    // Android ScrollView will not scrollTo certain offset when props change
    const callback = async () => {
      cb()
      if (Platform.OS === 'android') {
        if (this.state.index === 0) {
          this.props.horizontal
            ? this.scrollView.scrollTo({
                x: state.width,
                y: 0,
                animated: false
              })
            : this.scrollView.scrollTo({
                x: 0,
                y: state.height,
                animated: false
              })
        } else if (this.state.index === this.state.total - 1) {
          this.props.horizontal
            ? this.scrollView.scrollTo({
                x: state.width * this.state.total,
                y: 0,
                animated: false
              })
            : this.scrollView.scrollTo({
                x: 0,
                y: state.height * this.state.total,
                animated: false
              })
        }
      }
    }
    let index = state.index // 0
    if (!this.internals.offset)
      // Android not setting this onLayout first? https://github.com/leecade/react-native-swiper/issues/582
      this.internals.offset = {}
    const diff = offset[dir] - this.internals.offset[dir]
    const step = dir === 'x' ? state.width : state.height
    let loopJump = false

    // Do nothing if offset no change.
    if (!diff) return

    // Note: if touch very very quickly and continuous,
    // the variation of `index` more than 1.
    // parseInt() ensures it's always an integer
    index = parseInt(index + Math.round(diff / step))
    // 1
    if (this.props.loop) {
      if (index <= -1) {
        index = state.total - 1
        offset[dir] = step * state.total
        loopJump = true
      } else if (index >= state.total) {
        index = 0
        offset[dir] = step
        loopJump = true
      }
    }

    const newState = {}
    newState.index = index
    newState.loopJump = loopJump

    this.internals.offset = offset

    // only update offset in state if loopJump is true
    if (loopJump) {
      // when swiping to the beginning of a looping set for the third time,
      // the new offset will be the same as the last one set in state.
      // Setting the offset to the same thing will not do anything,
      // so we increment it by 1 then immediately set it to what it should be,
      // after render.
      if (offset[dir] === this.internals.offset[dir]) {
        newState.offset = { x: 0, y: 0 }
        newState.offset[dir] = offset[dir] + 1
        this.setState(newState, () => {
          this.setState({ offset: offset }, callback)
        })
      } else {
        newState.offset = offset
        this.setState(newState, callback)
      }
    } else {
      this.setState(newState, callback)
    }
  }

  /**
   * Scroll by index
   * @param  {number} index offset index
   * @param  {bool} animated
   */
  scrollBy = (index, animated = true) => {
      /*
         index 的值只有 -1:上一项 1:下一项
      */
    if (this.internals.isScrolling || this.state.total < 2) return

    const state = this.state
    // diff = 1+1+0=2
    const diff = (this.props.loop ? 1 : 0) + index + this.state.index
    let x = 0
    let y = 0
    if (state.dir === 'x') x = diff * state.width  // x=720
    if (state.dir === 'y') y = diff * state.height
     
     // 偏移到720，也就是显示第二项
    this.scrollView && this.scrollView.scrollTo({ x, y, animated })
    // 更新 scroll 状态 ,结束时再重设状态
    // update scroll state
    this.internals.isScrolling = true  //滚动中

    this.setState({
      autoplayEnd: false
    })

    // trigger onScrollEnd manually in android  
    // 手动触发  onMomentumScrollEnd={this.onScrollEnd}
    /* 经观察 onMomentumScrollEnd 在android上 
      调用this.scrollView.scrollTo（） 不会触发， 但滑动scrollView 便会触发
    */
    if (!animated || Platform.OS !== 'ios') {
      setImmediate(() => {
        this.onScrollEnd({
          nativeEvent: {
            position: diff
          }
        })
      })
    }
  }

  /**
   * Scroll to index
   * @param  {number} index page
   * @param  {bool} animated
   */

  scrollTo = (index, animated = true) => {
    if (
      this.internals.isScrolling ||
      this.state.total < 2 ||
      index == this.state.index
    )
      return

    const state = this.state
    const diff = this.state.index + (index - this.state.index)

    let x = 0
    let y = 0
    if (state.dir === 'x') x = diff * state.width
    if (state.dir === 'y') y = diff * state.height

    this.scrollView && this.scrollView.scrollTo({ x, y, animated })

    // update scroll state
    this.internals.isScrolling = true
    this.setState({
      autoplayEnd: false
    })

    // trigger onScrollEnd manually in android
    if (!animated || Platform.OS !== 'ios') {
      setImmediate(() => {
        this.onScrollEnd({
          nativeEvent: {
            position: diff
          }
        })
      })
    }
  }
  // scrollView 属性覆盖,只有一些函数才可以
  scrollViewPropOverrides = () => {
    const props = this.props
    let overrides = {}

    /*
    const scrollResponders = [
      'onMomentumScrollBegin',
      'onTouchStartCapture',
      'onTouchStart',
      'onTouchEnd',
      'onResponderRelease',
    ]
    */

    for (let prop in props) {
      // if(~scrollResponders.indexOf(prop)
      if (
        typeof props[prop] === 'function' &&
        prop !== 'onMomentumScrollEnd' &&
        prop !== 'renderPagination' &&
        prop !== 'onScrollBeginDrag'
      ) {
        let originResponder = props[prop]
        overrides[prop] = e => originResponder(e, this.fullState(), this)
      }
    }

    return overrides
  }

  /**
   * Render pagination
   * @return {object} react-dom // 创建分页指示器，根据当前的index,渲染activedDot
   */
  renderPagination = () => {
    // By default, dots only show when `total` >= 2
    if (this.state.total <= 1) return null

    let dots = []
    const ActiveDot = this.props.activeDot || (
      <View
        style={[
          {
            backgroundColor: this.props.activeDotColor || '#007aff',
            width: 8,
            height: 8,
            borderRadius: 4,
            marginLeft: 3,
            marginRight: 3,
            marginTop: 3,
            marginBottom: 3
          },
          this.props.activeDotStyle
        ]}
      />
    )
    const Dot = this.props.dot || (
      <View
        style={[
          {
            backgroundColor: this.props.dotColor || 'rgba(0,0,0,.2)',
            width: 8,
            height: 8,
            borderRadius: 4,
            marginLeft: 3,
            marginRight: 3,
            marginTop: 3,
            marginBottom: 3
          },
          this.props.dotStyle
        ]}
      />
    )
    for (let i = 0; i < this.state.total; i++) {
      dots.push(
        i === this.state.index
          ? React.cloneElement(ActiveDot, { key: i })
          : React.cloneElement(Dot, { key: i })
      )
    }

    return (
      <View
        pointerEvents="none"
        style={[
          styles['pagination_' + this.state.dir],
          this.props.paginationStyle
        ]}
      >
        {dots}
      </View>
    )
  }
  // 渲染标题
  renderTitle = () => {
    const child = this.state.children[this.state.index]
    const title = child && child.props && child.props.title
    return title ? (
      <View style={styles.title}>
        {this.state.children[this.state.index].props.title}
      </View>
    ) : null
  }

  // 

  renderNextButton = () => {
    let button = null

    if (this.props.loop || this.state.index !== this.state.total - 1) {
      button = this.props.nextButton || <Text style={styles.buttonText}>›</Text>
    }

    return (
      <TouchableOpacity
        onPress={() => button !== null && this.scrollBy(1)}
        disabled={this.props.disableNextButton}
      >
        <View>{button}</View>
      </TouchableOpacity>
    )
  }

  renderPrevButton = () => {
    let button = null

    if (this.props.loop || this.state.index !== 0) {
      button = this.props.prevButton || <Text style={styles.buttonText}>‹</Text>
    }

    return (
      <TouchableOpacity
        onPress={() => button !== null && this.scrollBy(-1)}
        disabled={this.props.disablePrevButton}
      >
        <View>{button}</View>
      </TouchableOpacity>
    )
  }

  renderButtons = () => {
    return (
      <View
        pointerEvents="box-none"
        style={[
          styles.buttonWrapper,
          {
            width: this.state.width,
            height: this.state.height
          },
          this.props.buttonWrapperStyle
        ]}
      >
        {this.renderPrevButton()}
        {this.renderNextButton()}
      </View>
    )
  }

  refScrollView = view => {
    this.scrollView = view
  }


  renderScrollView = pages => {
    return (
      <ScrollView
        ref={this.refScrollView}
        {...this.props}
        {...this.scrollViewPropOverrides()}
        contentContainerStyle={[styles.wrapperIOS, this.props.style]}
        contentOffset={this.state.offset}
        onScrollBeginDrag={this.onScrollBegin}
        onMomentumScrollEnd={this.onScrollEnd}
        onScrollEndDrag={this.onScrollEndDrag}
        style={this.props.scrollViewStyle}
      >
        {pages}
      </ScrollView>
    )
  }

  /**
   * Default render
   * @return {object} react-dom
   */
  render() {
    const { index, total, width, height, children } = this.state
    const {
      containerStyle,
      loop,
      loadMinimal,
      loadMinimalSize,
      loadMinimalLoader,
      renderPagination,
      showsButtons,
      showsPagination
    } = this.props
    // let dir = state.dir
    // let key = 0
    const loopVal = loop ? 1 : 0
    let pages = []
    
    const pageStyle = [{ width: width, height: height }, styles.slide]
    const pageStyleLoading = {
      width,
      height,
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center'
    }

    // For make infinite at least total > 1
    if (total > 1) {
      // Re-design a loop model for avoid img flickering
      pages = Object.keys(children)
      if (loop) {
        pages.unshift(total - 1 + '')
        pages.push('0')
      }

      pages = pages.map((page, i) => {
        if (loadMinimal) { // 默认false ，不走这步
          if (
            i >= index + loopVal - loadMinimalSize &&
            i <= index + loopVal + loadMinimalSize
          ) {
            return (
              <View style={pageStyle} key={i}>
                {children[page]}
              </View>
            )
          } else {
            return (
              <View style={pageStyleLoading} key={i}>
                {loadMinimalLoader ? loadMinimalLoader : <ActivityIndicator />}
              </View>
            )
          }
        } else {
          return (
            <View style={pageStyle} key={i}>
              {children[page]}
            </View>
          )
        }
      })
    } else {
      pages = (
        <View style={pageStyle} key={0}>
          {children}
        </View>
      )
    }

    // 第一次pages=[第三项,第一项,第二项,第三项,第一项] //

    return (
      <View style={[styles.container, containerStyle]} onLayout={this.onLayout}>
        {this.renderScrollView(pages)}
        {showsPagination &&
          (renderPagination
            ? renderPagination(index, total, this)
            : this.renderPagination())}
        {this.renderTitle()}
        {showsButtons && this.renderButtons()}
      </View>
    )
  }
}
```
