# 用 React 挂钩重写 Auth0 示例

> 原文：<https://dev.to/terrierscript/example-for-auth0-and-react-hooks-41e4>

Auth0 [默认 react 示例](https://github.com/auth0-samples/auth0-react-samples/tree/master/01-Login)不要使用 React 钩子。

我试图重写这个例子来使用 React 钩子。

## 完整示例

你可以在本资源库
[中阅读完整示例 https://github . com/terrier script/example-auth 0/tree/full-example](https://github.com/terrierscript/example-auth0/tree/full-example)

# 详细信息

## 1。创建上下文

首先，我创建了保存 auth 对象和一些 auth 结果状态的`AuthContext`。

```
// auth/AuthContext
import React, { createContext, useState, useContext } from 'react';

import { WebAuth } from 'auth0-js';
import { AUTH_CONFIG } from './auth0-variables';

const generateAuth = () =>
  new WebAuth({
    domain: AUTH_CONFIG.domain,
    clientID: AUTH_CONFIG.clientID,
    redirectUri: AUTH_CONFIG.callbackUrl,
    responseType: 'token id_token',
    scope: 'openid'
  });

const Auth0Context = createContext<ReturnType<typeof useContextValue>>(null);

const useAuthState = () => {
  return useState({
    accessToken: null,
    idToken: null,
    expiresAt: 0
  });
};

const useContextValue = () => {
  const [authState, updateAuthState] = useAuthState();
  return {
    auth0: generateAuth(),
    authState,
    updateAuthState
  };
};

export const Auth0Provider = ({ children }) => {
  const value = useContextValue();
  return (
    <Auth0Context.Provider value={value}>{children}</Auth0Context.Provider>
  );
};

export const useAuth0Context = () => {
  return useContext(Auth0Context);
}; 
```

## 2。创建上下文

接下来，生成`useAuth`。

几乎逻辑与 [`Auth.js`](https://github.com/auth0-samples/auth0-react-samples/blob/48c56b9bfbf84aedfc755bbee97ed20732bbfb3f/01-Login/src/Auth/Auth.js#L1-L101) 相同

但是`isAuthenticated`从`function`变成了`boolean`值与`useMemo`

```
// src/useAuth
import { useCallback, useMemo } from 'react';
import history from '../history'; // TODO: history may pass from props
import { useAuth0Context } from './AuthContext';

const useIsAuthenticated = expiresAt => {
  return useMemo(() => {
    return new Date().getTime() < expiresAt;
  }, [expiresAt]);
};

export const useAuth0 = () => {
  const { auth0, authState, updateAuthState } = useAuth0Context();

  const isAuthenticated = useIsAuthenticated(authState.expiresAt);

  const login = useCallback(() => {
    auth0.authorize();
  }, [auth0]);

  const logout = useCallback(() => {
    updateAuthState({
      accessToken: null,
      idToken: null,
      expiresAt: 0
    });
    localStorage.removeItem('isLoggedIn');

    auth0.logout({
      returnTo: window.location.origin
    });

    // navigate to the home route
    history.replace('/home');
  }, [auth0, updateAuthState]);

  const setSession = useCallback(
    authResult => {
      localStorage.setItem('isLoggedIn', 'true');

      let expiresAt = authResult.expiresIn * 1000 + new Date().getTime();
      updateAuthState({
        accessToken: authResult.accessToken,
        idToken: authResult.idToken,
        expiresAt: expiresAt
      });
      history.replace('/home');
    },
    [updateAuthState]
  );

  const renewSession = useCallback(() => {
    auth0.checkSession({}, (err, authResult) => {
      if (authResult && authResult.accessToken && authResult.idToken) {
        setSession(authResult);
      } else if (err) {
        logout();
        console.error(err);
        alert(
          `Could not get a new token (${err.error}: ${err.error_description}).`
        );
      }
    });
  }, []);

  const handleAuthentication = useCallback(() => {
    auth0.parseHash((err, authResult) => {
      if (authResult && authResult.accessToken && authResult.idToken) {
        setSession(authResult);
      } else if (err) {
        history.replace('/home');
        alert(`Error: ${err.error}. Check the console for further details.`);
      }
    });
  }, []);

  // retun some functions
  return {
    login,
    logout,
    handleAuthentication,
    isAuthenticated,
    renewSession
  };
}; 
```

## 3。修复`<Callback>`

在基本示例中，`handleAuthentication`在`router`中调用，就像[在](https://github.com/auth0-samples/auth0-react-samples/blob/48c56b9bfbf84aedfc755bbee97ed20732bbfb3f/01-Login/src/routes.js#L23-L26)中调用一样。

```
 <Route path="/callback" render={(props) => {
    handleAuthentication(props);
    return <Callback {...props} /> 
  }}/> 
```

我感觉这很棘手。
但是当使用钩子时，我们称之为`useEffect`

```
// Callback/Callback

import React, { useEffect } from 'react';
import loading from './loading.svg';
import { useAuth0 } from '../Auth/useAuth';

export const Callback = props => {
  const { handleAuthentication } = useAuth0();
  const { location } = props;
  useEffect(() => {
    if (/access_token|id_token|error/.test(location.hash)) {
      handleAuthentication();
    }
  }, [handleAuthentication, location]);

  const style = {
  //....
  };

  return (
    <div style={style}>
      <img src={loading} alt="loading" />
    </div>
  );
}; 
```

## [4](#4-fix-raw-ltappgt-endraw-and-raw-lthomegt-endraw-)。固定`<App>`和`<Home>`

`<App>`和`<Home>`也重写了。

`<App>`用`useEffect`称呼`renewSession`用

```
// App

import React, { useCallback, useEffect, useMemo } from 'react';
import { Navbar, Button } from 'react-bootstrap';
import './App.css';
import { useAuth0 } from './Auth/useAuth';

const useGoToHandler = history => {
  return useCallback(route => () => history.replace(`/${route}`), [history]);
};

export const App = ({ history }) => {
  const { login, logout, isAuthenticated, renewSession } = useAuth0();

  const goToHandler = useGoToHandler(history);
  useEffect(() => {
    if (localStorage.getItem('isLoggedIn') === 'true') {
      renewSession();
    }
  }, [renewSession]);

  return (
    <div>
      <Navbar fluid>
        <Navbar.Header>
          <Navbar.Brand>
            <a href="#">Auth0 - React</a>
          </Navbar.Brand>
          <Button
            bsStyle="primary"
            className="btn-margin"
            onClick={goToHandler('home')}
          >
            Home
          </Button>
          {!isAuthenticated && (
            <Button
              id="qsLoginBtn"
              bsStyle="primary"
              className="btn-margin"
              onClick={login}
            >
              Log In
            </Button>
          )}
          {isAuthenticated && (
            <Button
              id="qsLogoutBtn"
              bsStyle="primary"
              className="btn-margin"
              onClick={logout}
            >
              Log Out
            </Button>
          )}
        </Navbar.Header>
      </Navbar>
    </div>
  );
}; 
```

```
// Home/Home

import React from 'react';
import { useAuth0 } from '../Auth/useAuth';

export const Home = () => {
  const { login, isAuthenticated: isAuthenticated } = useAuth0();
  return (
    <div className="container">
      {isAuthenticated && <h4>You are logged in!</h4>}
      {!isAuthenticated && (
        <h4>
          You are not logged in! Please
          <a style={{ cursor: 'pointer' }} onClick={login}>
            Log In
          </a>
          to continue.
        </h4>
      )}
    </div>
  );
}; 
```

## 5。修复路由器

将路由器改写成这样。

*   路由器包装`<Auth0Provider>`。
*   逻辑移动了那个组件。
*   (琐碎)用`react-router` `<Switch>`。

```
// roter
import React from 'react';
import { Route, Router, Switch } from 'react-router-dom';
import { App } from './App';
import { Home } from './Home/Home';
import { Callback } from './Callback/Callback';
import history from './history';
import { Auth0Provider } from './Auth/AuthContext';

const Routes = () => {
  return (
    <Router history={history}>
      <Route path="/" render={props => <App {...props} />} />
      <Switch>
        <Route path="/home" render={props => <Home {...props} />} />
        <Route path="/callback" render={props => <Callback {...props} />} />
      </Switch>
    </Router>
  );
};

export const makeMainRoutes = () => {
  return (
    <Auth0Provider>
      <Routes />
    </Auth0Provider>
  );
}; 
```

## ⑥。设置 Auth0 和`npm start`

仅此而已！