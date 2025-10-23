---
title: React（十一）
date: 2020-06-02 21:40:33
categories: IT
tags:
    - IT，Web,Node，React
toc: true
thumbnail: https://cdn.kunkunzhang.top/redux.jpeg
---

​      前端框架，快速开发页面，函数式编程，与后端api快速搭建

<!--more-->

## Form组件

### react-hook-form

简单好看的react form表单

安装

```shell
npm install react-hook-form
```

使用

```react
import React from 'react';
import { useForm } from 'react-hook-form';

function App() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm();
  const onSubmit = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('firstName')} /> {/* register an input */}
      <input {...register('lastName', { required: true })} />
      {errors.lastName && <p>Last name is required.</p>}
      <input {...register('age', { pattern: /\d+/ })} />
      {errors.age && <p>Please enter number for age.</p>}
      <input type="submit" />
    </form>
  );
}
```

#### Register

useForm返回的register用于在对应的表单中注册字段（支持校验、嵌套字段、数组字段）

它返回一个包含以下四个属性的对象：

| Name     | Type      | Desc                                |
| -------- | --------- | ----------------------------------- |
| onChange | function  | input的onChange handler             |
| onBlur   | function  | input的onBlur handler               |
| ref      | React.Ref | 收集input实例，以支持error时的focus |
| name     | string    | input的name属性                     |

register通过它返回的对象接管了对input身上props（仅以上四个）的操作，这使得在大部分input（除checkbox、radio、file、submit、image、reset、button外）、select和textarea中，它都能像上例中情况一样直接使用。

#### UnRegister

如果form或者useFieldArrry中的参数[shouldUnregister](https://react-hook-form.com/docs/useform#shouldUnregister)为false，那么在form中的dom卸载之后对应的字段还在包括校验。此时可以使用 unregister api手动删除(包括校验)



controller

受控组件是通过 props 获取和设置其当前“状态”的组件。对于表单字段,该状态是该字段的当前值

```react
export default function App() {
  const { control, handleSubmit } = useForm({
    defaultValues: {
      textField: "",
      checkbox: false
    }
  });

  const onSubmit = (values) => alert(JSON.stringify(values));

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Controller
        control={control}
        name="textField"
        render={({ field }) => (
          // Material UI TextField already supports
          // `value` and `onChange`
          <TextField {...field} label="Text field" />
        )}
      />

      <Controller
        control={control}
        name="checkbox"
        render={({ field: { value, onChange } }) => (
          // Checkbox accepts its value as `checked`
          // so we need to connect the props here
          <FormControlLabel
            control={<Checkbox checked={value} onChange={onChange} />}
            label="I am a checkbox"
          />
        )}
      />

      <Button type="submit" variant="contained" color="primary">
        Submit
      </Button>
    </form>
  );
}
```

监听用form.watch('name')

#### useFieldArrry

如果form是数组类型，可以使用useFieldArrry, 返回的fields是数组的值，append用于给数组添加数据，remove用于移除数据, insert用于插入数据

useFieldArrry会给数组加一个id，用于识别数据中的数据(默认不使用index)

如果useFieldArrry中的参数[shouldUnregister](https://react-hook-form.com/docs/useform#shouldUnregister)为false，数据变少时需要手动卸载掉数组中数据。需要unregister掉最后一个数据，不能unregister删除的数据(因为会通过id识别到对应的数据)

```react
import React from "react";
import { useForm, useFieldArray } from "react-hook-form";

function App() {
  const { register, control, handleSubmit, reset, trigger, setError } = useForm({
    // defaultValues: {}; you can populate the fields by this attribute 
  });
  const { fields, append, remove } = useFieldArray({
    control,
    name: "test"
  });
  
  return (
    <form onSubmit={handleSubmit(data => console.log(data))}>
      <ul>
        {fields.map((item, index) => (
          <li key={item.id}>
            <input {...register(`test.${index}.firstName`)} />
            <Controller
              render={({ field }) => <input {...field} />}
              name={`test.${index}.lastName`}
              control={control}
            />
            <button type="button" onClick={() => remove(index)}>Delete</button>
          </li>
        ))}
      </ul>
      <button
        type="button"
        onClick={() => append({ firstName: "bill", lastName: "luo" })}
      >
        append
      </button>
      <input type="submit" />
    </form>
  );
}
```



接入yup

```react
import { yupResolver } from "@hookform/resolvers/yup";
import * as yup from "yup";
import { useForm } from "react-hook-form";

const schema = yup.object({
  date: yup.string().required().min(8).max(18),
});

export function FormAddress() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm({
    resolver: yupResolver(schema),

    // Not recommoned
    async defaultValues() {
      return {
        date: "2022-01-01",
      };
    },
  });

  return (
    <form action="#" onSubmit={handleSubmit((d) => console.log(d))}>
      <div>
        <input type="date" {...register("date")} />
        {/* @ts-ignore */}
        {errors.date && <p>{errors.date?.message}</p>}
      </div>
      <input type="submit" value="submit" />
    </form>
  );
}
```

接入zod



### redux-form

使用redux中的state管理form

```shell
npm install --save redux-form
```

使用

创建form的reducer

```react
import { createStore, combineReducers } from 'redux'
import { reducer as formReducer } from 'redux-form'

const rootReducer = combineReducers({
  // ...your other reducers here
  // you have to pass formReducer under 'form' key,
  // for custom keys look up the docs for 'getFormState'
  form: formReducer
})
```

使用reducer

```react
import React from 'react'
import { Field, reduxForm } from 'redux-form'

let ContactForm = props => {
  const { handleSubmit } = props
  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="firstName">First Name</label>
        <Field name="firstName" component="input" type="text" />
      </div>
      <div>
        <label htmlFor="lastName">Last Name</label>
        <Field name="lastName" component="input" type="text" />
      </div>
      <div>
        <label htmlFor="email">Email</label>
        <Field name="email" component="input" type="email" />
      </div>
      <button type="submit">Submit</button>
    </form>
  )
}

ContactForm = reduxForm({
  // a unique name for the form
  form: 'contact'
})(ContactForm)
```

在外部的组件中使用该form组件

```react
import React from 'react'
import ContactForm from './ContactForm'

class ContactPage extends React.Component {
  submit = values => {
    // print the form values to the console
    console.log(values)
  }
  render() {
    return <ContactForm onSubmit={this.submit} />
  }
}
```



### Formik

安装

```shell
npm install formik --save
```

使用

```react
 // Render Prop
 import React from 'react';
 import { Formik, Form, Field, ErrorMessage } from 'formik';
 
 const Basic = () => (
   <div>
     <h1>Any place in your app!</h1>
     <Formik
       initialValues={{ email: '', password: '' }}
       validate={values => {
         const errors = {};
         if (!values.email) {
           errors.email = 'Required';
         } else if (
           !/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i.test(values.email)
         ) {
           errors.email = 'Invalid email address';
         }
         return errors;
       }}
       onSubmit={(values, { setSubmitting }) => {
         setTimeout(() => {
           alert(JSON.stringify(values, null, 2));
           setSubmitting(false);
         }, 400);
       }}
     >
       {({ isSubmitting }) => (
         <Form>
           <Field type="email" name="email" />
           <ErrorMessage name="email" component="div" />
           <Field type="password" name="password" />
           <ErrorMessage name="password" component="div" />
           <button type="submit" disabled={isSubmitting}>
             Submit
           </button>
         </Form>
       )}
     </Formik>
   </div>
 );
 
 export default Basic;
```



### react-final-form

安装

```shell
npm install --save final-form react-final-form
```

使用

```react
import { Form, Field } from 'react-final-form'

const MyForm = () => (
  <Form
    onSubmit={onSubmit}
    validate={validate}
    render={({ handleSubmit }) => (
      <form onSubmit={handleSubmit}>
        <h2>Simple Default Input</h2>
        <div>
          <label>First Name</label>
          <Field name="firstName" component="input" placeholder="First Name" />
        </div>

        <h2>An Arbitrary Reusable Input Component</h2>
        <div>
          <label>Interests</label>
          <Field name="interests" component={InterestPicker} />
        </div>

        <h2>Render Function</h2>
        <Field
          name="bio"
          render={({ input, meta }) => (
            <div>
              <label>Bio</label>
              <textarea {...input} />
              {meta.touched && meta.error && <span>{meta.error}</span>}
            </div>
          )}
        />

        <h2>Render Function as Children</h2>
        <Field name="phone">
          {({ input, meta }) => (
            <div>
              <label>Phone</label>
              <input type="text" {...input} placeholder="Phone" />
              {meta.touched && meta.error && <span>{meta.error}</span>}
            </div>
          )}
        </Field>

        <button type="submit">Submit</button>
      </form>
    )}
  />
)
```

### zod

静态类型检查接口

安装

```shell
npm install zod       # npm
yarn add zod          # yarn
bun add zod           # bun
pnpm add zod          # pnpm
```

基础类型

```typescript
import { z } from "zod";

// primitive values
z.string();
z.number();
z.bigint();
z.boolean();
z.date();
z.symbol();

// empty types
z.undefined();
z.null();
z.void(); // accepts undefined

// catch-all types
// allows any value
z.any();
z.unknown();

// never type
// allows no values
z.never();
```

自定义类型错误校验函数和错误提示

```typescript
z.string().min(5, { message: "Must be 5 or more characters long" });
z.string().max(5, { message: "Must be 5 or fewer characters long" });
z.string().length(5, { message: "Must be exactly 5 characters long" });
z.string().email({ message: "Invalid email address" });
z.string().url({ message: "Invalid url" });
z.string().uuid({ message: "Invalid UUID" });
z.string().startsWith("https://", { message: "Must provide secure URL" });
z.string().endsWith(".com", { message: "Only .com domains allowed" });
z.string().datetime({ message: "Invalid datetime string! Must be UTC." });
```

错误提示

```typescript
const name = z.string({
  required_error: "Name is required",
  invalid_type_error: "Name must be a string",
});
```

数字类型自带校验函数

```typescript
z.number().gt(5);
z.number().gte(5); // alias .min(5)
z.number().lt(5);
z.number().lte(5); // alias .max(5)

z.number().int(); // value must be an integer

z.number().positive(); //     > 0
z.number().nonnegative(); //  >= 0
z.number().negative(); //     < 0
z.number().nonpositive(); //  <= 0

z.number().multipleOf(5); // Evenly divisible by 5. Alias .step(5)

z.number().finite(); // value must be finite, not Infinity or -Infinity
```

强制类型转换

```typescript
const schema = z.coerce.string();
schema.parse("tuna"); // => "tuna"
schema.parse(12); // => "12"
schema.parse(true); // => "true"

z.coerce.boolean().parse("tuna"); // => true
z.coerce.boolean().parse("true"); // => true
z.coerce.boolean().parse("false"); // => true
z.coerce.boolean().parse(1); // => true
z.coerce.boolean().parse([]); // => true

z.coerce.boolean().parse(0); // => false
z.coerce.boolean().parse(undefined); // => false
z.coerce.boolean().parse(null); // => false
```



### Yup

验证器，用在form上

使用

```react
import { object, string, number, date, InferType } from 'yup';

let userSchema = object({
  name: string().required(),
  age: number().required().positive().integer(),
  email: string().email(),
  website: string().url().nullable(),
  createdOn: date().default(() => new Date()),
});

// parse and assert validity
const user = await userSchema.validate(await fetchUser());

type User = InferType<typeof userSchema>;
/* {
  name: string;
  age: number;
  email?: string | undefined
  website?: string | null | undefined
  createdOn: Date
}*/
```

### class-validator

装饰器写法验证库

```javascript
import {
  validate,
  validateOrReject,
  Contains,
  IsInt,
  Length,
  IsEmail,
  IsFQDN,
  IsDate,
  Min,
  Max,
} from 'class-validator';

export class Post {
  @Length(10, 20)
  title: string;

  @Contains('hello')
  text: string;

  @IsInt()
  @Min(0)
  @Max(10)
  rating: number;

  @IsEmail()
  email: string;

  @IsFQDN()
  site: string;

  @IsDate()
  createDate: Date;
}

let post = new Post();
post.title = 'Hello'; // should not pass
post.text = 'this is a great post about hell world'; // should not pass
post.rating = 11; // should not pass
post.email = 'google.com'; // should not pass
post.site = 'googlecom'; // should not pass

validate(post).then(errors => {
  // errors is an array of validation errors
  if (errors.length > 0) {
    console.log('validation failed. errors: ', errors);
  } else {
    console.log('validation succeed');
  }
});

validateOrReject(post).catch(errors => {
  console.log('Promise rejected (validation failed). Errors: ', errors);
});
// or
async function validateOrRejectExample(input) {
  try {
    await validateOrReject(input);
  } catch (errors) {
    console.log('Caught promise rejection (validation failed). Errors: ', errors);
  }
}
```



### joi

```typescript
const Joi = require('joi');

const schema = Joi.object({
    username: Joi.string()
        .alphanum()
        .min(3)
        .max(30)
        .required(),

    password: Joi.string()
        .pattern(new RegExp('^[a-zA-Z0-9]{3,30}$')),

    repeat_password: Joi.ref('password'),

    access_token: [
        Joi.string(),
        Joi.number()
    ],

    birth_year: Joi.number()
        .integer()
        .min(1900)
        .max(2013),

    email: Joi.string()
        .email({ minDomainSegments: 2, tlds: { allow: ['com', 'net'] } })
})
    .with('username', 'birth_year')
    .xor('password', 'access_token')
    .with('password', 'repeat_password');
```

### superstruct

```typescript
import { assert, object, number, string, array } from 'superstruct'

const Article = object({
  id: number(),
  title: string(),
  tags: array(string()),
  author: object({
    id: number(),
  }),
})

const data = {
  id: 34,
  title: 'Hello World',
  tags: ['news', 'features'],
  author: {
    id: 1,
  },
}

assert(data, Article)
```



### valibot

```typescript
import * as v from 'valibot'; // 1.24 kB

// Create login schema with email and password
const LoginSchema = v.object({
  email: v.pipe(v.string(), v.email()),
  password: v.pipe(v.string(), v.minLength(8)),
});

// Infer output TypeScript type of login schema
type LoginData = v.InferOutput<typeof LoginSchema>; // { email: string; password: string }

// Throws error for `email` and `password`
v.parse(LoginSchema, { email: '', password: '' });

// Returns data as { email: string; password: string }
v.parse(LoginSchema, { email: 'jane@example.com', password: '12345678' });
```



### io-ts





### vest

声明式form验证库

使用

```javascript
import { create, test, enforce, only, warn, include, skipWhen } from "vest";
import wait from "wait";

const suite = create((data = {}, currentField) => {
  only(currentField);
  include("confirm").when("password");

  test("username", "Username is required", () => {
    enforce(data.username).isNotEmpty();
  });
  test("username", "Username is too short", () => {
    enforce(data.username).longerThan(2);
  });

  test.memo(
    "username",
    "Username already taken",
    () => {
      return doesUserExist(data.username);
    },
    [data.username]
  );

  test("password", "Password is required", () => {
    enforce(data.password).isNotEmpty();
  });
  test("password", "Password is too short", () => {
    enforce(data.password).longerThan(2);
  });
  test("password", "Password is weak. maybe add a number", () => {
    warn();
    enforce(data.password).matches(/[0-9]/);
  });

  // This means that "confirm" will not fail
  // before we start typing in it - even though it runs with "password"
  skipWhen(!data.confirm,() => {
    test("confirm", "Passwords do not match", () => {
      enforce(data.confirm).equals(data.password);
    });
  });

  // This test will only be evaluated once confirm had previous test runs
  // so that it will not light up unnecessarily 
  skipWhen(!suite.isTested("confirm"), () => {
    test("confirm", "Please confirm the password", () => {
      enforce(data.confirm).isNotEmpty();
    });
  })

  test("tos", () => {
    enforce(data.tos).isTruthy();
  });
});

export default suite;

async function doesUserExist(username) {
  await wait(1000);

  // fake taken username.
  enforce(parseInt(btoa(username), 36) % 3).notEquals(0);
}
```

引入组件中

```javascript
import React, { useState } from "react";
import Input from "./components/Input";
import Checkbox from "./components/Checkbox";
import Submit from "./components/Submit";

import classnames from "vest/classnames";
import suite from "./suite";
import "./styles.css";

export default function Form() {
  const [formstate, setFormstate] = useState({});
  const [, setUserNameLoading] = useState(false);

  const handleChange = (currentField, value) => {
    const nextState = { ...formstate, [currentField]: value };
    const result = suite(nextState, currentField);
    setFormstate(nextState);

    if (currentField === "username") {
      setUserNameLoading(true);
    }

    result.done(() => {
      setUserNameLoading(false);
    });
  };

  const cn = classnames(suite.get(), {
    invalid: "error",
    valid: "success",
    warning: "warning"
  });

  return (
    <form onSubmit={e => e.preventDefault()}>
      <Input
        name="username"
        onChange={handleChange}
        messages={suite.getErrors("username")}
        className={cn("username")}
        pending={suite.isPending("username")}
      />
      <Input
        name="password"
        onChange={handleChange}
        messages={suite
          .getErrors("password")
          .concat(suite.getWarnings("password"))}
        className={cn("password")}
      />
      <Input
        name="confirm"
        onChange={handleChange}
        messages={suite.getErrors("confirm")}
        className={cn("confirm")}
      />
      <Checkbox
        onChange={handleChange}
        name="tos"
        label="I have read and agreed to the terms of service"
        className={cn("tos")}
      />
      <Submit disabled={!suite.isValid()} />
    </form>
  );
}
```

## 代码编辑器

### @monaco-editor/react

在页面内插入文本编辑器，可以提供代码高亮、错误提示等功能

```shell
npm install @monaco-editor/react 
```

使用

```javascript
import Editor, { DiffEditor, useMonaco, loader } from "@monaco-editor/react";

function App() {
  const editorRef = useRef(null);

  useEffect(() => {
    if (monaco) {
      console.log("here is the monaco isntance:", monaco);
    }
  }, [monaco]);
  
  function handleEditorWillMount(monaco) {
    // here is the monaco instance
    // do something before editor is mounted
    monaco.languages.typescript.javascriptDefaults.setEagerModelSync(true);
  }
  
  function handleEditorDidMount(editor, monaco) {
    editorRef.current = editor; 
  }
  
  function handleEditorChange(value, event) {
    console.log("here is the current model value:", value);
  }
  
  function showValue() {
    alert(editorRef.current.getValue());
  }

  return (
   <>
     <button onClick={showValue}>Show value</button>
     <Editor
       height="90vh"
       defaultLanguage="javascript"
       defaultValue="// some comment"
       beforeMount={handleEditorWillMount}
       onMount={handleEditorDidMount}
			 onChange={handleEditorChange}
     />
   </>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

### react-ace

安装

```shell
yarn add react-ace ace-builds
```

使用

```react
import React from "react";
import { render } from "react-dom";
import AceEditor from "react-ace";

import "ace-builds/src-noconflict/mode-java";
import "ace-builds/src-noconflict/theme-github";
import "ace-builds/src-noconflict/ext-language_tools";

function onChange(newValue) {
  console.log("change", newValue);
}

// Render editor
render(
  <AceEditor
    mode="java"
    theme="github"
    onChange={onChange}
    name="UNIQUE_ID_OF_DIV"
    editorProps={{ $blockScrolling: true }}
  />,
  document.getElementById("example")
);
```

### 代码高亮

[react-syntax-highlighter](https://github.com/react-syntax-highlighter/react-syntax-highlighter)

安装

```shell
npm install react-syntax-highlighter --save
```

使用

```react
import { Prism as SyntaxHighlighter } from 'react-syntax-highlighter';
import { dark } from 'react-syntax-highlighter/dist/esm/styles/prism';
const Component = () => {
  const codeString = '(num) => num + 1';
  return (
    <SyntaxHighlighter language="javascript" style={dark}>
      {codeString}
    </SyntaxHighlighter>
  );
};
```



## 功能组件

### react-google-maps-api

安装

```shell
npm i -S @react-google-maps/api
```

使用

```react
import React from 'react'
import { GoogleMap, useJsApiLoader } from '@react-google-maps/api';

const containerStyle = {
  width: '400px',
  height: '400px'
};

const center = {
  lat: -3.745,
  lng: -38.523
};

function MyComponent() {
  const { isLoaded } = useJsApiLoader({
    id: 'google-map-script',
    googleMapsApiKey: "YOUR_API_KEY"
  })

  const [map, setMap] = React.useState(null)

  const onLoad = React.useCallback(function callback(map) {
    // This is just an example of getting and using the map instance!!! don't just blindly copy!
    const bounds = new window.google.maps.LatLngBounds(center);
    map.fitBounds(bounds);

    setMap(map)
  }, [])

  const onUnmount = React.useCallback(function callback(map) {
    setMap(null)
  }, [])

  return isLoaded ? (
      <GoogleMap
        mapContainerStyle={containerStyle}
        center={center}
        zoom={10}
        onLoad={onLoad}
        onUnmount={onUnmount}
      >
        { /* Child components, such as markers, info windows, etc. */ }
        <></>
      </GoogleMap>
  ) : <></>
}

export default React.memo(MyComponent)
```



### react-if

像使用v-if一样使用react

```react
import { If, Then, Else, When, Unless, Switch, Case, Default } from 'react-if';

import React from 'react';
import { Switch, Case, Default } from 'react-if';

const myNumber = 3;

const Example = () => (
  <div>
    <Switch>
      <Case condition={myNumber === 9}>This will be displayed if condition is matched</Case>
      <Case condition={myNumber > 1}>This will be displayed if condition is matched</Case>
      <Default>This will be displayed if no Case have matching condition</Default>
    </Switch>
  </div>
);
```

### React-is

判断react类型，dom是否是react组件

```react
import React from "react";
import * as ReactIs from "react-is";

class ClassComponent extends React.Component {
  render() {
    return React.createElement("div");
  }
}

const FunctionComponent = () => React.createElement("div");

const ForwardRefComponent = React.forwardRef((props, ref) =>
  React.createElement(Component, { forwardedRef: ref, ...props })
);

const Context = React.createContext(false);

ReactIs.isValidElementType("div"); // true
ReactIs.isValidElementType(ClassComponent); // true
ReactIs.isValidElementType(FunctionComponent); // true
ReactIs.isValidElementType(ForwardRefComponent); // true
ReactIs.isValidElementType(Context.Provider); // true
ReactIs.isValidElementType(Context.Consumer); // true
ReactIs.isValidElementType(React.createFactory("div")); // true
ReactIs.isContextConsumer(<ThemeContext.Consumer />); // true
ReactIs.isContextProvider(<ThemeContext.Provider />); // true
ReactIs.typeOf(<ThemeContext.Provider />) === ReactIs.ContextProvider; // true
ReactIs.typeOf(<ThemeContext.Consumer />) === ReactIs.ContextConsumer; // true
```



### echarts-for-react

安装

```shell
npm install --save echarts-for-react
```

使用

```react
import React from 'react';
import ReactECharts from 'echarts-for-react';  // or var ReactECharts = require('echarts-for-react');

<ReactECharts
  option={this.getOption()}
  notMerge={true}
  lazyUpdate={true}
  theme={"theme_name"}
  onChartReady={this.onChartReadyCallback}
  onEvents={EventsDict}
  opts={}
/>
```



### react-fiber-traverse

在fiber树中返回react node

安装

```shell
npm install react-fiber-traverse --save
```

使用

```jsx
import React from "react";
import { render } from "react-dom";
 
import { findNodeByComponentName, Utils } from "react-fiber-traverse";
 
// Sample component
// Say, if SomeComponentName looks like this -
function SomeComponentName() {
  return <div>Some text</div>;
}
 
// Render component
const rootElement = document.getElementById("root");
render(<SomeComponentName />, rootElement);
 
// Get root node
const rootFiberNode = Utils.getRootFiberNodeFromDOM(rootElement);
 
// Get component node
const someFiberNode = findNodeByComponentName(
  rootFiberNode,
  "SomeComponentName"
); // <- returns FiberNode for first usage of 'SomeComponentName'
 
console.log(someFiberNode.child.stateNode); // <- returns reference to the div
console.log(someFiberNode.child.stateNode.innerText); // <- returns 'Some text'
```



### react-router-cache-route

路由缓存

```javascript
import React from 'react'
import { HashRouter as Router, Route } from 'react-router-dom'
import CacheRoute, { CacheSwitch } from 'react-router-cache-route'

import List from './views/List'
import Item from './views/Item'

const App = () => (
  <Router>
    <CacheSwitch>
      <CacheRoute exact path="/list" component={List} />
      <Route exact path="/item/:id" component={Item} />
      <Route render={() => <div>404 Not Found</div>} />
    </CacheSwitch>
  </Router>
)

export default App
```

### react-keep-alive

保持当前页面的组件不被卸载

安装

```shell
npm install --save react-keep-alive
```

使用

```react
import React from 'react';
import ReactDOM from 'react-dom';
import {
  Provider,
  KeepAlive,
} from 'react-keep-alive';
import Test from './views/Test';

ReactDOM.render(
  <Provider>
    <KeepAlive name="Test">
      <Test />
    </KeepAlive>
  </Provider>,
  document.getElementById('root'),
);
```



### react-activation

React-activation类似于vue中的keep-alive，实现组件缓存

```javascript
// App.js

import React, { useState } from 'react'
import KeepAlive from 'react-activation'

function Counter() {
  const [count, setCount] = useState(0)

  return (
    <div>
      <p>count: {count}</p>
      <button onClick={() => setCount(count => count + 1)}>Add</button>
    </div>
  )
}

function App() {
  const [show, setShow] = useState(true)

  return (
    <div>
      <button onClick={() => setShow(show => !show)}>Toggle</button>
      {show && (
        <KeepAlive>
          <Counter />
        </KeepAlive>
      )}
    </div>
  )
}

export default App
```

可以在组件缓存或者卸载时做一些回调

```javascript
import KeepAlive, { useActivate, useUnactivate, withActivation } from 'react-activation'

@withActivation
class TestClass extends Component {
  ...
  componentDidActivate() {
    console.log('TestClass: componentDidActivate')
  }

  componentWillUnactivate() {
    console.log('TestClass: componentWillUnactivate')
  }
  ...
}
...
function TestFunction() {
  useActivate(() => {
    console.log('TestFunction: didActivate')
  })

  useUnactivate(() => {
    console.log('TestFunction: willUnactivate')
  })
  ...
}
...
function App() {
  ...
  return (
    {show && (
      <KeepAlive>
        <TestClass />
        <TestFunction />
      </KeepAlive>
    )}
  )
}
```



### classnames

当react原生动态添加多个className时就会报错，这时我们就可以利用classnames库添加多个className,这也是react官方推荐使用

安装

```shell
npm install classnames --save
```

支持动态导入

```react
import classnames from 'classnames'

<div className=classnames({
    'class1': true,
    'class2': true
    )>
</div>    
```

支持class动态传入变量，或者传入数组

```react
import classNames from 'classnames';

render() {
  const classStr = classNames({
    'class1': true,
    'class2': this.props.isCompleted,
    'class3': !this.props.isCompleted
    [a]: this.props.isCompleted
  });
  return (<div className={classStr}></div>);
}
```

### clsx

和classnames功能一样 但是体积更小

安装

```shell
npm install --save clsx
```

使用

```react
import clsx from 'clsx';
// or
import { clsx } from 'clsx';

// Strings (variadic)
clsx('foo', true && 'bar', 'baz');
//=> 'foo bar baz'

// Objects
clsx({ foo:true, bar:false, baz:isTrue() });
//=> 'foo baz'

// Objects (variadic)
clsx({ foo:true }, { bar:false }, null, { '--foobar':'hello' });
//=> 'foo --foobar'

// Arrays
clsx(['foo', 0, false, 'bar']);
//=> 'foo bar'

// Arrays (variadic)
clsx(['foo'], ['', 0, false, 'bar'], [['baz', [['hello'], 'there']]]);
//=> 'foo bar baz hello there'

// Kitchen sink (with nesting)
clsx('foo', [1 && 'bar', { baz:false, bat:null }, ['hello', ['world']]], 'cya');
//=> 'foo bar hello world cya'
```





### react-hot-loader

React-Hot-Loader 使用了 Webpack HMR API，针对 React 框架实现了对单个 component 的热替换，并且能够保持组件的 state。
React-Hot-Loader 在编译时会在每一个 React component 外封装一层，每一个这样的封装都会注册自己的 module.hot.accept 回调，它们会监听每一个 component 的更新，在当前 component 代码更新时只替换自己的模块，而不是整个替换 root component。
同时，React-Hot-Loader 对 component 的封装还会代理 component 的 state，所以当 component 替换之后依然能够保持之前的 state。

安装

```shell
npm install --save-dev react-hot-loader
```

 hot-loader 是基于 webpack-dev-server，所以还得安装 webpack-dev-server

```shell
npm install --save-dev webpack-dev-server
```

首先还是要让 webpack-dev-server 打开。

```javascript
var webpack = require('webpack');
var WebpackDevServer = require('webpack-dev-server');
var config = require('./webpack.config');

new WebpackDevServer(webpack(config), {
  publicPath: config.output.publicPath,
  hot: true,
  historyApiFallback: true
}).listen(3000, 'localhost', function (err, result) {
  if (err) {
    return console.log(err);
  }

  console.log('Listening at http://localhost:3000/')
});
```

然后在 webpack 的配置文件里添加 react-hot-loader。

```javascript
var webpack = require('webpack');

module.exports = {
  // 修改 entry
  entry: [
    // 写在入口文件之前
    "webpack-dev-server/client?http://0.0.0.0:3000",
    "webpack/hot/only-dev-server",
    // 这里是你的入口文件
    "./src/app.js",
  ],
  output: {
    path: __dirname,
    filename: "build/js/bundle.js",
    publicPath: "/build"
  },
  module: {
    loaders: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        // 在这里添加 react-hot，注意这里使用的是loaders，所以不能用 query，应该把presets参数写在 babel 的后面
        loaders: ['react-hot', 'babel?presets[]=react,presets[]=es2015']
      }
    ]
  },
  // 添加插件
  plugins: [
    new webpack.HotModuleReplacementPlugin()
  ]
```



### react-hot-toast

全屏的通知组件

安装

```shell
npm install react-hot-toast
```

使用

```react
import toast, { Toaster } from 'react-hot-toast';

const notify = () => toast('Here is your toast.');

const App = () => {
  return (
    <div>
      <button onClick={notify}>Make me a toast</button>
      <Toaster />
    </div>
  );
};
```

### notistack



```react
import { SnackbarProvider, useSnackbar } from 'notistack';

// wrap your app
<SnackbarProvider>
  <App />
  <MyButton />
</SnackbarProvider>

const MyButton = () => {
  const { enqueueSnackbar, closeSnackbar } = useSnackbar();
  return <Button onClick={() => enqueueSnackbar('I love hooks')}>Show snackbar</Button>;
};
```



### remotion

用react写video

使用

```react
import { useCurrentFrame } from "remotion";
 
export const MyVideo = () => {
  const frame = useCurrentFrame();
 
  return (
    <div
      style={{
        flex: 1,
        textAlign: "center",
        fontSize: "7em",
      }}
    >
      The current frame is {frame}.
    </div>
  );
};
```

配置帧数

```react
import { useVideoConfig } from "remotion";
 
export const MyVideo = () => {
  const { fps, durationInFrames, width, height } = useVideoConfig();
 
  return (
    <div
      style={{
        flex: 1,
        textAlign: "center",
        fontSize: "7em",
       }}
      >
      This {width}px x {height}px video is {durationInFrames / fps} seconds long.
    </div>
  );
};
```

### react-player

react视频播放组件，比原生的h5 video标签好用

安装

```shell
npm install react-player # or yarn add react-player
```

使用

```react
import React, {useEffect} from 'react'
import ReactPlayer from 'react-player'

const App = () => {
  
  useEffect(() => {
    ReactPlayer.canPlay(url)
  },[url])

  return <ReactPlayer url={url} light="xxx.jpg" onReady={} onError={} />
}
```

https://cookpete.com/react-player/



### revideo

**Revideo** 是一个开源的视频编辑框架，基于 **TypeScript** 开发，旨在通过代码创建和编辑视频。

它是从令人惊叹的 Motion Canvas运动画布 编辑器衍生而来的，旨在将其从一个独立的应用程序转变为一种库，开发者可以利用它来构建完整的视频编辑应用程序

想象一下，通过编写几行代码，你就可以：

- • 创建一段炫酷的产品宣传视频；
- • 自动生成一个定制化的视频模板；
- • 与其他 AI 图像生成技术结合，生成动态、多样的视频内容。

更重要的是，Revideo 不仅支持创建视频模板，还可以通过部署 API 的方式实现自动化渲染，这为企业和开发者提供了前所未有的便利

#### 核心功能

- • **代码化创建视频模板**：可以使用 TypeScript 编写代码，定义视频的结构、样式、动画等，就像在写一个文档一样。
- • **API 部署与渲染**：将你的视频模板部署到服务器，并调用 API 端点进行渲染，即可生成视频文件。
- • **提供 React 播放器组件**：Revideo 提供了一个 React 播放器组件，可以让你在浏览器中实时预览视频效果。
- • **支持动态输入**：可以将视频模板中的某些元素设置为动态输入，例如视频片段、图片、文字等，从而创建出更加个性化的视频。
- • **高度扩展性**：Revideo 是基于 TypeScript 开发的，开发者可以轻松地扩展功能，比如：自定义特效和动画、集成其他 AI 工具（如 SD、即梦、可灵等）生成动态内容、添加特定的格式转换或优化模块。

```react
import {Audio, Img, Video, makeScene2D} from '@revideo/2d';
import {all, chain, createRef, waitFor} from '@revideo/core';

export default makeScene2D('scene', function* (view) {
  const logoRef = createRef<Img>();

  yield view.add(
    <>
      <Video
        src={'https://revideo-example-assets.s3.amazonaws.com/stars.mp4'}
        size={['100%', '100%']}
        play={true}
      />
      <Audio
        src={'https://revideo-example-assets.s3.amazonaws.com/chill-beat.mp3'}
        play={true}
        time={17.0}
      />
    </>,
  );

  yield* waitFor(1);

  view.add(
    <Img
      width={'1%'}
      ref={logoRef}
      src={
        'https://revideo-example-assets.s3.amazonaws.com/revideo-logo-white.png'
      }
    />,
  );

  yield* chain(
    all(logoRef().scale(40, 2), logoRef().rotation(360, 2)),
    logoRef().scale(60, 1),
  );
});
```





### react-motion



### input-opt

密码输入框

```shell
'use client'
import { OTPInput, SlotProps } from 'input-otp'
<OTPInput
  maxLength={6}
  containerClassName="group flex items-center has-[:disabled]:opacity-30"
  render={({ slots }) => (
    <>
      <div className="flex">
        {slots.slice(0, 3).map((slot, idx) => (
          <Slot key={idx} {...slot} />
        ))}
      </div>

      <FakeDash />

      <div className="flex">
        {slots.slice(3).map((slot, idx) => (
          <Slot key={idx} {...slot} />
        ))}
      </div>
    </>
  )}
/>

// Feel free to copy. Uses @shadcn/ui tailwind colors.
function Slot(props: SlotProps) {
  return (
    <div
      className={cn(
        'relative w-10 h-14 text-[2rem]',
        'flex items-center justify-center',
        'transition-all duration-300',
        'border-border border-y border-r first:border-l first:rounded-l-md last:rounded-r-md',
        'group-hover:border-accent-foreground/20 group-focus-within:border-accent-foreground/20',
        'outline outline-0 outline-accent-foreground/20',
        { 'outline-4 outline-accent-foreground': props.isActive },
      )}
    >
      {props.char !== null && <div>{props.char}</div>}
      {props.hasFakeCaret && <FakeCaret />}
    </div>
  )
}

// You can emulate a fake textbox caret!
function FakeCaret() {
  return (
    <div className="absolute pointer-events-none inset-0 flex items-center justify-center animate-caret-blink">
      <div className="w-px h-8 bg-white" />
    </div>
  )
}

// Inspired by Stripe's MFA input.
function FakeDash() {
  return (
    <div className="flex w-10 justify-center items-center">
      <div className="w-3 h-1 rounded-full bg-border" />
    </div>
  )
}

// tailwind.config.ts for the blinking caret animation.
const config = {
  theme: {
    extend: {
      keyframes: {
        'caret-blink': {
          '0%,70%,100%': { opacity: '1' },
          '20%,50%': { opacity: '0' },
        },
      },
      animation: {
        'caret-blink': 'caret-blink 1.2s ease-out infinite',
      },
    },
  },
}

// Small utility to merge class names.
import { clsx } from 'clsx'
import { twMerge } from 'tailwind-merge'

import type { ClassValue } from 'clsx'

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```

### react-input-mask

为用户输入提供掩码

```shell
npm install react-input-mask --save
```

使用

```react
import { useState } from 'react';
import InputMask from 'react-input-mask';
function PhoneInput(props) {
  return (
    <InputMask 
      mask='(+1) 999 999 9999' 
      value={props.value} 
      onChange={props.onChange}>
    </InputMask>
  );
}
function App() {
  const [phone, setPhone] = useState('');
  const handleInput = ({ target: { value } }) => setPhone(value);
  return (
    <div>
      <PhoneInput 
        value={phone} 
        onChange={handleInput}>
      </PhoneInput>
      <div style={{paddingTop: '12px'}}>Phone: {phone}</div>
    </div>
  );
}
export default App;
```

### react-imask

和react-input-mask功能相同

```react
import { useRef } from 'react';
import { IMaskInput } from 'react-imask';

// use ref to get access to internal "masked = ref.current.maskRef"
const ref = useRef(null);
const inputRef = useRef(null);
<IMaskInput
  mask={Number}
  radix="."
  value="123"
  unmask={true} // true|false|'typed'
  ref={ref}
  inputRef={inputRef}  // access to nested input
  // DO NOT USE onChange TO HANDLE CHANGES!
  // USE onAccept INSTEAD
  onAccept={
    // depending on prop above first argument is
    // `value` if `unmask=false`,
    // `unmaskedValue` if `unmask=true`,
    // `typedValue` if `unmask='typed'`
    (value, mask) => console.log(value)
  }
  // ...and more mask props in a guide

  // input props also available
  placeholder='Enter number here'
/>
```



### React-filepond

react上传文件组件

https://github.com/pqina/react-filepond



### react-datasheet

react中构建像excel一样功能的库

安装

```shell
npm install react-datasheet --save
```

使用

```react
import ReactDataSheet from 'react-datasheet';
// Be sure to include styles at some point, probably during your bootstrapping
import 'react-datasheet/lib/react-datasheet.css';

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      grid: [
        [{ value: 1 }, { value: 3 }],
        [{ value: 2 }, { value: 4 }],
      ],
    };
  }
  render() {
    return (
      <ReactDataSheet
        data={this.state.grid}
        valueRenderer={cell => cell.value}
        onCellsChanged={changes => {
          const grid = this.state.grid.map(row => [...row]);
          changes.forEach(({ cell, row, col, value }) => {
            grid[row][col] = { ...grid[row][col], value };
          });
          this.setState({ grid });
        }}
      />
    );
  }
}
```

### fortune-sheet

https://github.com/ruilisi/fortune-sheet

```react
import React from 'react';
import ReactDOM from 'react-dom';
import { Workbook } from "@fortune-sheet/react";
import "@fortune-sheet/react/dist/index.css"

ReactDOM.render(
  <Workbook data={[{ name: "Sheet1" }]} />,
  document.getElementById('root')
);
```





### tinycolor

对color进行操作,类似于moment操作时间

```javascript
var colors = tinycolor("#f00").analogous();

colors.map(function(t) { return t.toHexString(); }); // [ "#ff0000", "#ff0066", "#ff0033", "#ff0000", "#ff3300", "#ff6600" ]
```



### react-chrono

react的时间轴组件

安装

```shell
## install with yarn
yarn add react-chrono

## or with npm
npm install react-chrono
```

使用

```react
  import React from "react"
  import { Chrono } from "react-chrono";

  const Home = () => {
    const items = [{
      title: "May 1940",
      cardTitle: "Dunkirk",
      url: "http://www.history.com",
      cardSubtitle:"Men of the British Expeditionary Force (BEF) wade out to..",
      cardDetailedText: "Men of the British Expeditionary Force (BEF) wade out to..",
      media: {
        type: "IMAGE",
        source: {
          url: "http://someurl/image.jpg"
        }
      }
    }, ...];

    return (
      <div style={{ width: "500px", height: "400px" }}>
        <Chrono items={items} />
      </div>
    )
  }
```



### rrweb

录制web操作、重放

rrweb 主要由 3 部分组成：

- **[rrweb-snapshot](https://github.com/rrweb-io/rrweb/tree/master/packages/rrweb-snapshot/)**，包含 snapshot 和 rebuild 两个功能。snapshot 用于将 DOM 及其状态转化为可序列化的数据结构并添加唯一标识；rebuild 则是将 snapshot 记录的数据结构重建为对应的 DOM。
- **[rrweb](https://github.com/rrweb-io/rrweb)**，包含 record 和 replay 两个功能。record 用于记录 DOM 中的所有变更（mutation）；replay 则是将记录的变更按照对应的时间一一重放。
- **[rrweb-player](https://github.com/rrweb-io/rrweb/tree/master/packages/rrweb-player/)**，为 rrweb 提供一套 UI 控件，提供基于 GUI 的暂停、快进、拖拽至任意时间点播放等功能。

安装

```shell
npm install --save rrweb
```

rrweb 同时提供 commonJS 和 ES modules 两种格式的打包文件，易于和常见的打包工具配合使用。

使用，录制

```javascript
rrweb.record({
  emit(event) {
    // 用任意方式存储 event
  },
});

let stopFn = rrweb.record({
  emit(event) {
    if (events.length > 100) {
      // 当事件数量大于 100 时停止录制
      stopFn();
    }
  },
});
```

回放

```javascript
const replayer = new rrweb.Replayer(events);

// 播放
replayer.play();

// 从第 3 秒的内容开始播放
replayer.play(3000);

// 暂停
replayer.pause();

// 暂停至第 5 秒处
replayer.pause(5000);

// 销毁播放器 (提示： 这个操作不可逆)
replayer.destroy();
```



