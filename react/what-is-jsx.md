# What is JSX?

In truth, JSX itself does not drive the rendering of React components to HTML. JSX is simply an XML-like syntax for javascript objects. It resides a layer about React, which transforms the JSX syntax to function calls to `React.createElement`. A small example of this transformation would be:

```
// JSX
<Button className="btn-red" style={{fontSize: '18px'}}>
    Click here
</Button>
```

```
// To React function
React.createElement(
    Button,
    {
        className: 'btn-red',
        style: {
            fontSize: '18px',
        },
    },
    'Click here',
);
```

Great, now we've gotten a layer closer to React, meaning we're a step closer to how the data is actually rendered. However, what is the result of this `React.createElement` function? What is happening with this result?

`React.createElement` is by itself another transformation function that will return a simple javascript object with all the above information nicely structured:

```
{
    $$typeof: Symbol('react.element'),
    type: Button,
    key: null,
    props: {
        className: 'btn-red',
        style: {
            fontSize: '18px',
        },
        children: 'Click here'
    },
    ref: null,
}
```

The `$$typeof` value is a React safety mechanism in order distinguish a component object to a plain object, say added from a user provided JSON. But other than that, the object is very similar to the object provided to `React.Component`.

We can make a few logical conclusions, that may have seemed detached when referencing JSX code:

1. The `key` property is occasionally required when generating a set of the same Component, but we do not have access to this value from inside the component. This seems more logical now, seeing that it is not defined as part of the `props` key and is therefore not accessible through `this.props.key`.
2. Even though children are defined as being the inner content of a component in JSX, they are accessible through `this.props.children` from inside the component. Through `React.CreateElement`, the children are pushed into the `props` key of the component object. Understanding this, you can even call your children as a prop to your component:

```
<Button children="Click here"></Button>

React.createElement(
    Button,
    {
        children: 'Click here',
    },
    null
);
```

Though this is discouraged. If you defined children as both a prop and as inner content to your component, the `children` prop value will be overwritten by the inner content through the JSX to object transformation.



-----




const SYMBOL = React.createElement().$$typeof;

class App extends React.Component {
    render() {
        return {
            $$typeof: SYMBOL,
            type: Button,
            key: null,
            props: {
                children: 'disable'
            },
            ref: null
        };
    }

    render() {
        {
            $$typeof: SYMBOL,
            type: 'div',
            key: null,
            props: {
                children: [
                    {
                        $$typeof: SYMBOL,
                        type: Container,
                        key: null,
                        props: {
                            backgroundColor: 'red',
                            children: {
                                $$typeof: SYMBOL,
                                type: Button,
                                key: null,
                                props: {
                                    children: 'disable'
                                },
                                ref: null
                            },
                        },
                        ref: null
                    },
                    {
                        $$typeof: SYMBOL,
                        type: Container,
                        key: null,
                        props: {
                            backgroundColor: 'blue',
                            children: {
                                $$typeof: SYMBOL,
                                type: Button,
                                key: null,
                                props: {
                                    children: 'disable'
                                },
                                ref: null
                            }
                        },
                        ref: null
                    }
                ],
            },
            ref: null,
        }


        return React.createElement('div', null, [
            React.createElement(Container, {
                backgroundColor: 'red'
            }, React.createElement(Button, {}, 'enable')),
            React.createElement(Container, {
                backgroundColor: 'blue'
            }, {
                $$typeof: SYMBOL,
                type: Button,
                key: null,
                props: {
                    children: 'disable'
                },
                ref: null
            })
        ]);
    }
}

class Container extends React.Component {
    render() {
        return (
            <div style={{
                display: 'inline-block',
                padding: 10,
                margin: 10,
                backgroundColor: this.props.backgroundColor
            }}>
                {this.props.children}
            </div>
        );
    }
}

class Button extends React.Component {
    render() {
        return (
            <button style={{
                appearance: 'none',
                backgroundColor: 'white',
                border: 'none',
                boxShadow: 'none',
                borderRadius: 4,
                display: 'block',
                padding: '3px 10px'
            }}>
                {this.props.children}
            </button>
        );
    }
}

ReactDOM.render(
    <App/>, document.getElementById('main')
);
