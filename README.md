# React Native Navigator Performance

There are two options for navigation in RN: Navigator and NavigatorIOS.

We’re using Navigator. Why here:
https://facebook.github.io/react-native/docs/navigator-comparison.html#content

So we’ve built our beautiful navbar. All good bar one major issue: Page transitions are slow and it takes a second or so for the app to *React* to a user touch and navigate to their selected page.

Why
Navigator animations are controlled by the JavaScript thread. If there is a heavy animation or big component to process, this will lock up the JavaScript thread and it won't be unable to send data to the main thread - breaking the UX.

How we solved this
The InteractionManager

“InteractionManager allows long-running work to be scheduled after any interactions/animations have completed.”

Applications can schedule tasks to run after interactions with the following:

```js
InteractionManager.runAfterInteractions(() => {
 // ...long-running synchronous task...
});
```

If we can schedule events to load after the javascript thread is clear, we can first render a nice lightweight loading screen before we render the full component. This is probably best described in an example:

```js

var NavChild = React.createClass({
  getInitialState: function () {
    return this.state = {loadingView: true};
  },

  componentDidMount() {
   InteractionManager.runAfterInteractions(() => {
     this.setState({loadingView: false});
   });
  },

  render: function() {
    if (this.state.loadingView) {
     return this._renderLoadingView();
   }
    return (
       <Text>HEAVY COMPONENT</Text>
    );
  },
  
  _renderLoadingView() {
    return (
      <View>
        <Text>Nice loading view</Text>
      </View>
    );
  }
});
```

One more thing. Taking your app out of development mode will significantly speed things up. Nice description of how to do this at the bottom of this blog by Herman Schaaf: http://herman.asia/building-a-flashcard-app-with-react-native
