---
title: React+Redux
date: 2018-01-05 16:26:51
tags: React
categories: JavaScript
---

### actions.js
```
//action 创建函数
const action = (type, payload = {}) => ({ type, payload });

export const GET_ADS = 'GET_ADS';
export const ADD_ADS = 'ADD_ADS';
export const UPDATE_ADS = 'UPDATE_ADS';
export const DEL_ADS = 'DEL_ADS';

export const getAds = () => action(GET_ADS);
export const addAds = payload => action(ADD_ADS, payload);
export const updateAds = payload => action(UPDATE_ADS, payload);
export const delAds = payload => action(DEL_ADS, payload);
```

### reducers.js

```
import { combineReducers } from 'redux';
import * as actionTypes from './actions';

export const adsResults = (state = [], action) => {
  switch (action.type) {
    case actionTypes.GET_ADS:
      return [
        ...state,
        action.payload
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
      return Object.assign(state, action.payload)

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

const result = combineReducers({
  ads: adsResults,
})

const entities = combineReducers({
  ads: adsEntities,
})

export default combineReducers({
  result,
  entities,
})

export const combineData = (result, entities) => (result.map(item => entities[item]))

```

### data.js

```
export const ads = [
  {
    "id": 1,
    "image": "http://api.handsomehan.cn:10013/media/20111009043132953_4NWLIlb.jpg",
    "title": "月季花",
    "description": null,
    "page_view": 0
  },
  {
    "id": 2,
    "image": "http://api.handsomehan.cn:10013/media/tooopen_sy_121089197226_awrU7X6.jpg",
    "title": "花",
    "description": null,
    "page_view": 0
  }
]
```

### store.js

```
import { normalize, schema } from 'normalizr';

import { ads } from '../data';

const data = {
  ads: ads,
}

export const adSchema = new schema.Entity('ads');

export const stateSchema = {
  ads: [adSchema],
}

export const initialState = normalize(data, stateSchema)

```
### index.js

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import Ad from './Ad';
import registerServiceWorker from './registerServiceWorker';

import { Provider } from 'react-redux';
import { createStore } from 'redux';
import reducer from './reducers';

import { initialState } from './store';

export const store = createStore(
  reducer,
  initialState,
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
)

ReactDOM.render(
  <Provider store={store}>
    <Ad />
  </Provider>
  , document.getElementById('root'));
registerServiceWorker();

```

### Ad.js

```
import React, { Component } from 'react';
import { bindActionCreators } from 'redux';
import { connect } from 'react-redux';

import { getAds, addAds, delAds, updateAds } from './actions';
import { combineData } from './reducers';

class Ad extends Component {
  AddAd = () => {
    const payload = {
      id: this.props.adsResult.length + 1,
      title: '多肉植物',
      description: '我会一朵🌺 ，可爱的🌺 。',
      image: 'http://api.handsomehan.cn:10013/media/20111009043132953_4NWLIlb.jpg',
      page_view: 0,
    }

    this.props.addAds(payload)
  }

  delAd = item => {
    this.props.delAds(item)
  }

  updateAd = item => {
    const payload = {
      id: item.id,
      title: '睡莲',
      description: '我会一朵🌺 ，可爱的🌺 。',
      image: 'http://api.handsomehan.cn:10013/media/20111009043132953_4NWLIlb.jpg',
      page_view: 0,
    }

    this.props.updateAds(payload)
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
    getAds,
    addAds,
    delAds,
    updateAds,
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
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}

```
