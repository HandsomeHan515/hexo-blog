---
title: 使用React-router，Get详情时的一种解决办法
date: 2018-01-06 17:43:38
tags: Redux
categories: JavaScript
---

    当redux中有数据时，直接用redux中的数据，当redux中没有数据时，执行request获取detail详情。

```
class FlowerDetail extends Component {
  constructor(props) {
    super(props);

    this.state = {
      title: undefined,
      image: undefined,
      description: undefined,
    }
  }

  getFlowerDetail = flowerID => {
    request({
      url: `${address.flowers}${flowerID}/`,
      hasCert: false,
    })
      .then(resp => {
        const { title, image, description } = resp

        this.setState({
          title,
          image,
          description,
        })
      })
      .catch(err => {
        console.log('detail err: %o', err)
      })
  }

  componentWillMount() {
    const { flowerID } = this.props.match.params
    const { flowersResult, flowersEntities } = this.props

    if (flowersResult.length) {
      console.log('use redux data')
      const { title, image, description } = flowersEntities[flowerID]

      this.setState({
        title,
        image,
        description,
      })
    } else {
      console.log('use get data')
      this.getFlowerDetail(flowerID)
    }
  }

  render() {
    const { title, image, description } = this.state
    return (
      <div style={{ textAlign: 'center' }}>
        <img style={{ width: '70%', height: 400, marginTop: 30 }} src={image} alt={title} />
        <h3>名字：{title}</h3>
        <p>描述：{description}</p>
      </div>
    );
  }
}

const mapStateToProps = state => {
  return {
    flowersResult: state.result.flowers,
    flowersEntities: state.entities.flowers,
  }
}

export default connect(
  mapStateToProps,
)(FlowerDetail)

```