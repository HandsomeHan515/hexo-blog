---
title: Redux+Saga
date: 2018-01-06 13:53:50
tags: React
categories: JavaScript
---

### actions.js

```
//action 创建函数
const action = (type, payload = {}) => ({ type, payload });

//ads
export const GET_AD = 'GET_AD';
export const GET_ADS = 'GET_ADS';

export const getAd = () => action(GET_AD);
export const getAds = () => action(GET_ADS);

export const UPDATE_AD = 'UPDATE_AD';
export const UPDATE_ADS = 'UPDATE_ADS';

export const updateAd = payload => action(UPDATE_AD, payload);
export const updateAds = payload => action(UPDATE_ADS, payload);

export const ADD_AD = 'ADD_AD';
export const ADD_ADS = 'ADD_ADS';

export const addAd = payload => action(ADD_AD, payload);
export const addAds = payload => action(ADD_ADS, payload);


export const DEL_AD = 'DEL_AD';
export const DEL_ADS = 'DEL_ADS';

export const delAd = payload => action(DEL_AD, payload);
export const delAds = payload => action(DEL_ADS, payload);
```

### api.js

```
import { normalize } from 'normalizr';

import { request, address } from '../service/';
import * as state from './store';

const get = (config, schema, name) => request(config)
  .then(resp => {
    const schemaData = normalize(resp, schema)
    const { count, previous, next } = schemaData.result
    Object.assign(schemaData.entities[name], { count, previous, next })
    return schemaData
  });

const base = config => request(config).then(resp => resp);

export const list = config => base(config);

export const create = config => base(
  Object.assign({}, {
    method: 'POST',
  }, config)
);

export const update = config => base(
  Object.assign({}, {
    method: 'PATCH',
  }, config)
);

export const del = config => base(
  Object.assign({}, {
    method: 'DELETE',
    isJson: false,
  }, config)
)

export const getAds = () => get({
  url: address.ads,
  hasCert: false,
}, { results: [state.adSchema] }, 'ads');

export const updateAds = payload => update({
  url: `${address.ads}${payload.id}/`,
  body: JSON.stringify(payload),
  hasCert: false,
})

export const createAds = payload => create({
  url: address.ads,
  body: JSON.stringify(payload),
  hasCert: false,
})

export const delAds = payload => del({
  url: `${address.ads}${payload.id}/`,
  hasCert: false,
})
```

### reducers.js

```
import * as actionTypes from './actions';

export const adsResults = (state = [], action) => {
  switch (action.type) {
    case actionTypes.GET_ADS:
      return [
        ...state,
        ...action.payload.result.results
      ]

    case actionTypes.ADD_ADS:
      return [
        ...state,
        action.payload.id
      ]

    case actionTypes.DEL_ADS:
      let newData = state.slice()
      return newData.filter(item => item !== action.payload.id)

    default:
      return state
  }
}

export const adsEntities = (state = {}, action) => {
  switch (action.type) {
    case actionTypes.GET_ADS:
      return Object.assign(state, action.payload.entities.ads)

    case actionTypes.ADD_ADS:
      return Object.assign(state, {
        [action.payload.id]: action.payload
      })

    case actionTypes.UPDATE_ADS:
      // Why? 使用Object.assign()redu中的数据该表，但是界面不渲染
      // return Object.assign(state, {
      //   [action.payload.id]: action.payload
      // })
      return {
        ...state,
        [action.payload.id]: action.payload
      }

    default:
      return state
  }
}
```

### sagas.js

```
import { take, call, put, fork, all } from 'redux-saga/effects';

import * as Action from './actions';
import * as Api from './api';

export function* getAds() {
  while (true) {
    yield take(Action.GET_AD);

    try {
      const ads = yield call(Api.getAds);
      yield put({ type: Action.GET_ADS, payload: ads });
    } catch (error) {
      console.log('ads error: %o', error)
    }
  }
}

export function* updateAds() {
  while (true) {
    const { payload } = yield take(Action.UPDATE_AD);

    try {
      const ads = yield call(Api.updateAds, payload);
      yield put({ type: Action.UPDATE_ADS, payload: ads })
    } catch (error) {
      console.log('ads error: %o', error)
    }
  }
}

export function* addAds() {
  while (true) {
    const { payload } = yield take(Action.ADD_AD);

    try {
      const ads = yield call(Api.createAds, payload);
      yield put({ type: Action.ADD_ADS, payload: ads })
    } catch (error) {
      console.log('ads error: %o', error)
    }
  }
}

export function* delAds() {
  while (true) {
    const { payload } = yield take(Action.DEL_AD);

    try {
      yield call(Api.delAds, payload);
      yield put({ type: Action.DEL_ADS, payload: payload })
    } catch (error) {
      console.log('ads error: %o', error)
    }
  }
}

export function* getFlowers() {
  while (true) {
    yield take(Action.GET_AD);

    try {
      let flowers = yield call(Api.getFlowers);
      yield put({ type: Action.GET_FLOWERS, payload: flowers });
    } catch (error) {
      console.log('flowers error: %o', error)
    }
  }
}

const watchingSagas = [
  fork(getAds),
  fork(updateAds),
  fork(addAds),
  fork(delAds),
  fork(getFlowers),
]

export default function* root() {
  yield all([
    ...watchingSagas,
  ])
}
```

### store.js

```
import { normalize, schema } from 'normalizr';

import { ads } from './data';

const data = {
  ads: ads,
}

export const adSchema = new schema.Entity('ads');

export const stateSchema = {
  ads: [adSchema],
  flowers: [flowerSchema],
}

export const initialState = normalize(data, stateSchema)
```

### service.js

```
const serviceDomain = 'http://api.handsomehan.cn:10013';

export const address = {
  ads: `${serviceDomain}/v1/ads/`,
  flowers: `${serviceDomain}/v1/flowers/`,
}

export const request = config => {
  config = Object.assign({}, {
    url: '',
    body: undefined,
    method: 'GET',
    hasCert: true,
    isJson: true,
    isBlob: false,
    hasHeader: true
  }, config)

  let elements = {
    method: config.method,
    body: config.body,
    headers: undefined
  }

  if (config.hasHeader) {
    elements.headers = {
      Accept: 'application/json',
      'Content-Type': 'application/json',
    }
  }

  if (config.hasCert) {
    elements.headers.Authorization = `JWT ${token}`
  }

  // console.log('config: %o, elements: %o', config, elements)

  return (
    fetch(config.url, elements)
      .then((response) => {
        // console.log("Get response:%o", response)
        if (!response.ok) {
          // console.log("Get fail response:%o", response)
          let error = new Error(response.statusText || 'Something bas happen')
          error.response = response
          throw error
        }

        if (config.isBlob) return response.blob()
        if (config.isJson) return response.json()

        return response.text()
      })
  )
}
```

### Ads.js

```
import React, { Component } from 'react';
import { bindActionCreators } from 'redux';
import { connect } from 'react-redux';

import { getAd, updateAd, addAd, delAd, getFlower } from './actions';
import { combineData } from './reducers';

import { image } from './data';


class Ad extends Component {
  componentWillMount() {
    this.props.getAd()
    this.props.getFlower()
  }

  AddAd = () => {
    const payload = {
      title: '多肉植物',
      description: '我会一朵🌺 ，可爱的🌺 。',
      image: image,
    }

    this.props.addAd(payload)
  }

  delAd = item => {
    this.props.delAd(item)
  }

  updateAd = item => {
    const payload = {
      id: item.id,
      title: '多肉',
      description: '我是一朵小红花 ，可爱的🌺 。',
    }
    this.props.updateAd(payload)
  }

  render() {
    const { adsResult, adsEntities } = this.props
    const ads = combineData(adsResult, adsEntities)

    return (
      <div>
        {
          ads.map(item => {
            return (
              <div key={item.id}>
                <div>
                  {item.title}
                  {item.description}
                </div>
                <img style={{ height: 180, width: 300 }} src={item.image} alt={item.title} />
                <button onClick={() => this.delAd(item)}>删除</button>
                <button onClick={() => this.updateAd(item)}>更新</button>
              </div>
            )
          })
        }
        <button onClick={this.AddAd}>添加广告</button>
      </div>
    );
  }
}

const mapStateToProps = state => {
  return {
    adsResult: state.result.ads,
    adsEntities: state.entities.ads,
  }
}

const mapDispatchToProps = dispatch => {
  return bindActionCreators({
    getAd,
    updateAd,
    addAd,
    getFlower,
    delAd,
  }, dispatch)
}

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(Ad)
```

### package.json

```
{
  "name": "flower",
  "version": "0.1.0",
  "private": true,
  "devDependencies": {
    "react-scripts": "1.0.17"
  },
  "dependencies": {
    "normalizr": "^3.2.4",
    "react": "^16.2.0",
    "react-dom": "^16.2.0",
    "react-redux": "^5.0.6",
    "redux": "^3.7.2",
    "redux-saga": "^0.16.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```