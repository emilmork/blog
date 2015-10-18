---
layout:     post
title:      React.cloneElement
date:       2015-10-15 11:21:29
summary:    React.addons.cloneWithProps is depricated in favour for React.cloneElement.
categories: javascript react
---

React.addons.cloneWithProps has been deprecated in favour for [React.cloneElement](https://facebook.github.io/react/docs/top-level-api.html#react.cloneelement). The behaviour is
almost the same, but cloneElement does not merge className or style.  

```javascript
React.cloneElement(element, props, ...children);
```

The example below shows how to create a stateless component that center all child components with React.cloneElement and Object.assign.


```javascript
// h2 and h3 will get textAlign: 'center'

var withExtraStyle = (p, style) => {
  return React.cloneElement(p, Object.assign(p.props.style, style));
}

const Centered = (p) => {
  return <div> { React.Children.map(p.children, (e) => {
      return withExtraStyle(e, {textAlign: 'center'})) 
  } </div>
}

const Title = (p) => <h2 style={styles.text}>{p.children}</h2>

const App = () => {
  return (
      <Centered>
          <h2 style={styles.text}>Title text - centered</h2>
          <h3 style={styles.text}>Subtitle text - centered</h3>
      </Centered>
  );
}

var styles = {
  container: {
    flex: 1
  },
  text: {
    fontSize: 25,
    color: 'gray'
  }
}


export default App;
```

[React 0.14 changelog](https://facebook.github.io/react/blog/2015/09/10/react-v0.14-rc1.html)


---

