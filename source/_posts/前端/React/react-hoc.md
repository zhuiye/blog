---
title: react 的高阶组件
date: 2020-03-21 21:25:45
tags: react 
---

## 前言

 初学react 之时，对于react 的高阶组件,没有那么深刻的体会，用它只是过滤几个属性？创建几个不同的组件？当时的我由于一把梭，封装组件上少之又上，简直根本用不上。后来学习到组件化，封装越来越多，也深刻体会到它的作用。接下来简单的说下它。

## 高阶组件的定义

说白了，他就是个函数，通过传入"一封装的组件类" 和 一系列用来产生对组件类产生不同行为的**参数**,返回一个React.Class 组件,大致的模式如下:

```js
  function createHoc=(ReactClassComponent)=>{

    return withClass extends {

      componentDidMount {

      }
      
      render(){
        return <>
                ReactClassComponent
               </>
      }
    }
  }
```

## 实际运用

### 场景1

近期的项目中,有一些页面需要实现微信的授权。由于我们需要把微信授权的行为封装起来，但是，应该怎么注入到每个页面之中呢？一开始，我本想从react-route 的路由去解决的，就是 “**路由拦截**”,一般大家喜欢称之为“**路由守卫**”，**Vue** 中倒是非常贴心的实现了该功能，但是 react-route 中就需要我们自己封装实现了。因为我的**懒惰**,又搜索了一下，就看到了高阶组件实现的方案。

### 运用如下

高阶组件 withWeiXinAuth

```js
    import React from "react";
    import { isWeiXin } from "utils/envCheck";
    import getSearchString from "utils/searchString";
    import { getOpenId } from "./apply/service";
    import { Toast } from "antd-mobile";
    import { CenterContainer } from "components";

    export default function withWeiXinAuth(
      WrapperComponent: React.FunctionComponent
    ) {
      return class WeiXinAuth extends React.Component {
        state = {
          loading: false
        };
        componentDidMount() {
          // 微信的授权操作代码 这里实现
        }

        render() {
          if (isWeiXin && !localStorage.getItem("openId")) {
            return <CenterContainer>等待授权中~</CenterContainer>;
          }

          return (
           
              <WrapperComponent {...this.props} />
          );
        }
      };
    }

```

对于需要授权的页面，我们只需要如下代码

```js
   export default withWeiXinAuth(AppHome)
```



### 场景2

进行后端的管理系统开发的时候，通常会遇到很多相似乎的表格，（如：审核通过表格，未通过表格，审核申请表格，这些表格的字段，大体相同以及筛选条件也相同 ）对于这样的场景，我们自然是把它给抽象出一个组件出来，然后利用一个高阶函数，对这个组件进行包裹，从而产生上述三个不同的表格.

### 实现

```js

import React, { Component } from 'react';
import router from 'umi/router';
import { Button, Form, Row, Col, Input, Select } from 'antd';
import AllTable from '@/components/Account/All-Table';
import styles from './index.less';
import { queryCkPolicyReviewList } from './service';

const FormItem = Form.Item;

export default function withReviewPage(config) {
  /* config.type= unReview|pass|reject
    status:0 1 2
  */
  const columns = [
    // 一些相同的列字段
    {
      title: '操作',
      dataIndex: 'actions',
      key: 'actions',
      align: 'center',
      render: (text, record) => (
        <Button
          size="small"
          onClick={() =>
            router.push(`/qkt/ckmanage/review/detail/${record.id}?status=${config.status}`)
          }
        >
          {config.type === 'unReview' ? '审核' : '查看详情'}
        </Button>
      ),
    },
  ];
  if (config.status !== 0) {
    columns.splice(5, 0, {
      title: '审核人',
      dataIndex: 'operator',
      key: 'operator',
      align: 'center',
    });
  }

  @Form.create()
  class Page extends Component {
    state = {
      data: {},
      loading: false,
    };

    componentDidMount() {
      this.fetchList(1, 10);
    }

    handleFormReset = () => {
    
    };

    fetchList = async (pn, pc) => {
    };

    handleStandardTableChange = pagination => {
      this.fetchList(pagination.current, pagination.pageSize);
    };

    SearchForm = () => {
      const {
        form: { getFieldDecorator },
      } = this.props;

      return (
        <Form layout="inline">
          <Row gutter={{ md: 8, lg: 24, xl: 48 }}>
            <Col xl={6} lg={8} sm={24}>
              <FormItem label="xx">
                {getFieldDecorator('search')(<Input placeholder="请输入xxx姓名或者手机号码" />)}
              </FormItem>
            </Col>
           
            <Col xl={6} lg={24} sm={24}>
              <FormItem>
                <Button
                  onClick={() => {
                    this.fetchList(1, 10);
                  }}
                >
                  查询
                </Button>
                <Button style={{ marginLeft: 8 }} onClick={this.handleFormReset}>
                  重置
                </Button>
              </FormItem>
            </Col>
          </Row>
        </Form>
      );
    };

    render() {
      const { data, loading } = this.state;
      return (
        <React.Fragment>
          <div className={styles.tableListForm}>{this.SearchForm()}</div>
          <br />
          <AllTable
            columns={columns}
            rowKey={record => record.id}
            data={data}
            loading={loading}
            onChange={this.handleStandardTableChange}
            style={{ minHeight: 500 }}
            size="middle"
          />
        </React.Fragment>
      );
    }
  }

  return Page;
}
```
使用，只需如下

```js 
  export default withReviewPage({type:'unReview',status:1})
  export default withReviewPage({type:'pass',status:2})
  export default withReviewPage({type:'reject',status:3})

```

## 结束语 

React 高阶组件的场景还有很多，比如redux中对**容器组**件注入 state ,和dispatch,就采用的高阶组件的方式，当然react-route 中的Route 组件 也一样。 可以看出高阶组件对于封装简直是一大利器.后续有实现场景再进行补充



