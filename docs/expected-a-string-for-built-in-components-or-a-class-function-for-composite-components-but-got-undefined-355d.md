# 应为字符串(对于内置组件)或类/函数(对于复合组件)，但得到:undefined

> 原文：<https://dev.to/bellancaf/expected-a-string-for-built-in-components-or-a-class-function-for-composite-components-but-got-undefined-355d>

大家好！

我正在经历一个小(大)恼人的问题:D

我们市场上的系统无法获取某些产品，但可以成功地获取其他产品。

->[https://www.feralhorses.co.uk/artworks/i-belong-to-you](https://www.feralhorses.co.uk/artworks/i-belong-to-you)(管用)
->[https://www.feralhorses.co.uk/artworks/parallel-expressions](https://www.feralhorses.co.uk/artworks/parallel-expressions)(不管用)

```
import React, { Component, PropTypes } from 'react';
import { connect } from 'react-redux';
import { bindActionCreators } from 'redux';
import * as authActions from '../actions/authActions';
import * as lotActions from '../actions/lotActions';
import { requestCurrentLot } from '../utils/crudHelpers';
import { logActions, createLog } from '../utils/logHelpers';
import { checkToken } from '../utils/authHelpers';
import SingleArtworkPrimary from './SingleArtworkPrimary';
import SingleArtworkSecondary from './SingleArtworkSecondary';
import {
  requestArtworkByName,
  requestSingleArtworkGeneral,
  requestArtworks
} from '../utils/crudHelpers';

class SingleArtworkContainer extends Component {
  constructor(props) {
    super(props);

    this.getSingleArtwork = this.getSingleArtwork.bind(this);    
    this.requestArtistPrimaryArtworks = this.requestArtistPrimaryArtworks.bind(this);
    this.readUrl = this.readUrl.bind(this);

    this.state = {
      singleArtwork: null,
      artistArtworks: []      
    };
  }

  componentDidMount() {
    if (checkToken()) {
      document.body.classList.add('is-auth');
    }
    this.getSingleArtwork(this.readUrl());  
  }

  readUrl() {
    const browserPath = window.location.pathname;
    return browserPath.split('/')[2];
  }

  getSingleArtwork(artworkTitle) {
    requestSingleArtworkGeneral(artworkTitle)
      .then((response) => {
        if(response.data.result){
          const { artwork } = response.data;
          if (artwork.status === 2) {
            this.requestArtistPrimaryArtworks(artwork.primary_lot_id, artwork.artist.pretty_url);
          }
          this.setState({
            singleArtwork: artwork
          });
          if(checkToken()) {
            createLog(logActions.primaryMarketArtwork, `Artwork: ${artwork.artwork_id}-${artwork.artwork_title} by ${artwork.artist_name}`);
          }
        } else {
          authActions.forwardUserTo('/not-found');
        }
      })
      .catch((err) => {
        console.log(err);
      });
  }

  requestArtistPrimaryArtworks(lot_id, prettyUrl) {
    requestArtworks(1, lot_id, 3, {artist_pretty_url: prettyUrl})
      .then((response) => {
        response.data.primary_lot_artworks.map((artwork, key) => {
          this.state.artistArtworks.push(artwork);
        });
        this.setState({
          isLoading: false          
        });
      })
      .catch((err) => {
        console.log(err);
      });
  }

  render() {
    if(this.state.singleArtwork === null) {
      return (
        <div style={{ minHeight: '95vh' }}>
          <h3>Loading</h3>
        </div>
      );
    } else if (this.state.singleArtwork.status === 2) {
      return (
        <div>
          <SingleArtworkPrimary
            artwork={this.state.singleArtwork}
            artistArtworks={this.state.artistArtworks}
            />
        </div>
      );
    } else {
      return (
        <div>
          <SingleArtworkSecondary 
            artwork={this.state.singleArtwork}
            />
        </div>
      );
    }
  }
}

const mapStateToProps = (state, ownProps) => ({
  lot: state.lots.lot
});

const mapDispatchToProps = (dispatch) => ({
  actions: {
    lotActions: bindActionCreators(lotActions, dispatch)
  }
});

SingleArtworkContainer.propTypes = {
  artwork: PropTypes.object,
  from: PropTypes.string,
  lot: PropTypes.object
};

export default connect(mapStateToProps, mapDispatchToProps)(SingleArtworkContainer); 
```

我的第一个捕捉错误是什么安慰了它

**预期的行为是什么？**
鉴于这是完全相同的代码，老实说，我对我得到一个 React 错误感到惊讶，因为两个页面后面的代码完全相同:(

哪个版本的 React 和哪个浏览器/操作系统会受到此问题的影响？这在以前版本的 React 中有效吗？
所有浏览器
React 15.3.0(但也发生在我们 UAT 机器上用 react 16.8)