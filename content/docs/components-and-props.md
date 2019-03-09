---
id: components-and-props
title: কম্পোনেন্টস ও Props
permalink: docs/components-and-props.html
redirect_from:
  - "docs/reusable-components.html"
  - "docs/reusable-components-zh-CN.html"
  - "docs/transferring-props.html"
  - "docs/transferring-props-it-IT.html"
  - "docs/transferring-props-ja-JP.html"
  - "docs/transferring-props-ko-KR.html"
  - "docs/transferring-props-zh-CN.html"
  - "tips/props-in-getInitialState-as-anti-pattern.html"
  - "tips/communicate-between-components.html"
prev: rendering-elements.html
next: state-and-lifecycle.html
---

কম্পোনেন্ট আপনাকে UI কে স্বাধীন ও পুনরায় ব্যবহারযোগ্য করে ভাঙতে দেয়,এবং মনে করুন প্রত্যেকটি অংশ বিচ্ছিন্ন থাকে। এই পৃষ্ঠাটি কম্পোনেন্ট এর ধারণার সাথে পরিচয় করাবে। আপনি এখানে [বিস্তারিত কম্পোনেন্ট API রেফারেন্স পাবেন](/docs/react-component.html)

ধারণাগতভাবে, কম্পোনেন্টস অনেকটা জাভাস্কিপ্ট এর ফাংশন এর মত। তারা ইচ্ছামত ইনপুট নেয় (তাদের "props" বলে) এবং স্ক্রিনএ যা দেখাবে তা বর্ণিত React elements ফেরত দেয়।

## ফাংশন এবং ক্লাস কম্পোনেন্ট {#function-and-class-components}

জাভাস্ক্রিপ্ট ফাংশন লিখে কম্পোনেন্ট সবচেয়ে সহজভাবে বর্ণনা করা যায়ঃ

```js
function Welcome(props) {
  return <h1>হ্যালো, {props.name}</h1>;
}
```

এই ফাংশনটি React কম্পোনেন্ট হিসাবে বৈধ কারণ এটি একটি "props" (যা প্রোপার্টিস বুঝায়) অবজেক্ট আর্গুমেন্ট ডাটাসহ React element এ ফেরত পাঠায় । আমরা এই রকম কম্পোনেন্টসকে "function components" বলি কারণ তারা সোজাসুজি জাভাস্ক্রিপ্ট এর ফাংশনস।

আপনি চাইলে [ES6 ক্লাস](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) ব্যবহার করেও কম্পোনেন্ট তৈরি করতে পারবেন।

```js
class Welcome extends React.Component {
  render() {
    return <h1>হ্যালো, {this.props.name}</h1>;
  }
}
```

উপরের দুটি কম্পোনেন্ট React's এর দৃষ্টিতে একই।

Class এর কিছু অধিক বৈশিষ্ট্য আছে যা আমরা [পরবর্তী অধ্যায়ে](/docs/state-and-lifecycle.html) আলোচনা করব। ততক্ষণ পর্যন্ত আমরা সংক্ষিপ্ত রাখার জন্য ফাংশনাল কম্পোনেন্ট ব্যবহার করব।

## কম্পোনেন্টকে রেন্ডার করা {#rendering-a-component}

পূর্বে, আমরা এমন React elements er সম্মুখীন হয়েছি যা DOM ট্যাগস কে চিত্রিত করেঃ

```js
const element = <div />;
```

তবে, elements কে user-defined কম্পোনেন্টসেও প্রকাশিত করা যায়ঃ

```js
const element = <Welcome name="Sara" />;
```

When React sees an element representing a user-defined component, it passes JSX attributes to this component as a single object. We call this object "props".

For example, this code renders "Hello, Sara" on the page:

```js{1,5}
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

[](codepen://components-and-props/rendering-a-component)

Let's recap what happens in this example:

1. We call `ReactDOM.render()` with the `<Welcome name="Sara" />` element.
2. React calls the `Welcome` component with `{name: 'Sara'}` as the props.
3. Our `Welcome` component returns a `<h1>Hello, Sara</h1>` element as the result.
4. React DOM efficiently updates the DOM to match `<h1>Hello, Sara</h1>`.

>**Note:** Always start component names with a capital letter.
>
>React treats components starting with lowercase letters as DOM tags. For example, `<div />` represents an HTML div tag, but `<Welcome />` represents a component and requires `Welcome` to be in scope.
>
>To learn more about the reasoning behind this convention, please read [JSX In Depth](/docs/jsx-in-depth.html#user-defined-components-must-be-capitalized).

## Composing Components {#composing-components}

Components can refer to other components in their output. This lets us use the same component abstraction for any level of detail. A button, a form, a dialog, a screen: in React apps, all those are commonly expressed as components.

For example, we can create an `App` component that renders `Welcome` many times:

```js{8-10}
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

[](codepen://components-and-props/composing-components)

Typically, new React apps have a single `App` component at the very top. However, if you integrate React into an existing app, you might start bottom-up with a small component like `Button` and gradually work your way to the top of the view hierarchy.

## Extracting Components {#extracting-components}

Don't be afraid to split components into smaller components.

For example, consider this `Comment` component:

```js
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

[](codepen://components-and-props/extracting-components)

It accepts `author` (an object), `text` (a string), and `date` (a date) as props, and describes a comment on a social media website.

This component can be tricky to change because of all the nesting, and it is also hard to reuse individual parts of it. Let's extract a few components from it.

First, we will extract `Avatar`:

```js{3-6}
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
  );
}
```

The `Avatar` doesn't need to know that it is being rendered inside a `Comment`. This is why we have given its prop a more generic name: `user` rather than `author`.

We recommend naming props from the component's own point of view rather than the context in which it is being used.

We can now simplify `Comment` a tiny bit:

```js{5}
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <Avatar user={props.author} />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

Next, we will extract a `UserInfo` component that renders an `Avatar` next to the user's name:

```js{3-8}
function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}
```

This lets us simplify `Comment` even further:

```js{4}
function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

[](codepen://components-and-props/extracting-components-continued)

Extracting components might seem like grunt work at first, but having a palette of reusable components pays off in larger apps. A good rule of thumb is that if a part of your UI is used several times (`Button`, `Panel`, `Avatar`), or is complex enough on its own (`App`, `FeedStory`, `Comment`), it is a good candidate to be a reusable component.

## Props are Read-Only {#props-are-read-only}

Whether you declare a component [as a function or a class](#function-and-class-components), it must never modify its own props. Consider this `sum` function:

```js
function sum(a, b) {
  return a + b;
}
```

Such functions are called ["pure"](https://en.wikipedia.org/wiki/Pure_function) because they do not attempt to change their inputs, and always return the same result for the same inputs.

In contrast, this function is impure because it changes its own input:

```js
function withdraw(account, amount) {
  account.total -= amount;
}
```

React is pretty flexible but it has a single strict rule:

**All React components must act like pure functions with respect to their props.**

Of course, application UIs are dynamic and change over time. In the [next section](/docs/state-and-lifecycle.html), we will introduce a new concept of "state". State allows React components to change their output over time in response to user actions, network responses, and anything else, without violating this rule.
