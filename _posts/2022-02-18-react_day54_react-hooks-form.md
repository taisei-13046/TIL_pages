---
layout: post
title: "react hook formについて" 
date: 2022-02-18 
category: read 
excerpt: ""
---

## やったこと
react-hook-formの概要と実際に使ってみる

### React Hook Form
参考資料
- [React Hook Form ライブラリの使い方！フォームのバリデーションを実装しよう【 TypeScript 】](https://xn--eckhu0e2b3a6i6dsh.net/react-hook-form/)
- [React Hook Form](https://react-hook-form.com/get-started)

#### React Hook Formとは
React でフォームのバリデーションを実装するためのライブラリです。公式サイトでは、「高性能で柔軟かつ拡張可能な使いやすいフォームバリデーションライブラリ」と謳われています。  

非制御コンポーネント (Uncontrolled Components) を useForm フックに登録（ register) することで、フォームのフィールド情報を検証・収集できることをコンセプトに据えています。  

**制御コンポーネントと非制御コンポーネントの違い**  

制御・非制御コンポーネントの違いは、フォームのフィールド値を『** React 側の state として保持する**』か『**そのフィールド自身で保持する**』か  
フィールドの値を保持する管理元が異なります。  

非制御コンポーネント（ Uncontrolled Component ）とは 、フォームのフィールドそのもので値を保持します。 フィールドから値を取得するには、 useRef フックなどを使って、事前に参照を渡しておく必要があります。

```jsx
// 制御コンポーネントの具体例

import { useState } from "react";

export default function App() {
  const [state, setState] = useState<string>("");

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    alert("名前: " + state);
  };

  const handleChange = (e: React.FormEvent<HTMLInputElement>) => {
    setState(e.currentTarget.value);
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        名前
        <input type="text" value={state} onChange={(e) => handleChange(e)} />
      </label>
      <button>実行する</button>
    </form>
  );
}
```

```jsx
// 非制御コンポーネントの具体例

import { useRef } from "react";

export default function App() {
  const input = useRef<HTMLInputElement>(null);

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    alert("名前: " + input.current);
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        名前
        <input type="text" ref={input} />
      </label>
      <button>実行する</button>
    </form>
  );
}
```

##### sample code
```tsx
import React from "react";
import { useForm, SubmitHandler } from "react-hook-form";

interface Inputs {
  example: string;
  exampleRequired: string;
}

function App() {
  const {
    register,
    handleSubmit,
    watch,
    formState: { errors },
  } = useForm<Inputs>();
  const onSubmit: SubmitHandler<Inputs> = (data) => console.log(data);
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input defaultValue="test" {...register("example")} />
      <input {...(register("exampleRequired"), { required: true })} />
      {errors.exampleRequired && <span>this field is required</span>}
      <input type="submit" />
    </form>
  );
}
```

```tsx
import { useForm, SubmitHandler } from "react-hook-form";
```
**SubmitHandler** は、 TypeScript で書く場合に必要になる型定義です。 submit イベントに関連して実行する関数の型宣言に使います。  

```tsx
interface Inputs {
  example: string;
  exampleRequired: string;
}
```
フォームの入力値についての型定義。useFormフックを書く時に使う。  

```tsx
  const {
    register,
    handleSubmit,
    watch,
    formState: { errors },
  } = useForm<Inputs>();
  const onSubmit: SubmitHandler<Inputs> = (data) => console.log(data);
```

### register
[register](https://react-hook-form.com/api/useform/register)  

**Props**
- onChange	
- onBlur	
- ref
- name

**Options**
- ref
```jsx
<input {...register("test")} />
```

optionを追加したい場合は、
```jsx
<input
  {...register("test", {
    required: 'error message' // JS only: <p>error message</p> TS only support string
  })}
/>
```
のように追加する。

追加可能なoption:
- **required**
  - A Boolean which, if true, indicates that the input must have a value before the form can be submitted. You can assign a string to return an error message in the errors object.  
- **maxLength**
  - The maximum length of the value to accept for this input.	
- **minLength**
  - The minimum length of the value to accept for this input.	
- **max**
  - The maximum value to accept for this input.	
- **min**
  - The minimum value to accept for this input.	
- **pattern**
  - The regex pattern for the input.
- **validate**
  - You can pass a callback function as the argument to validate, or you can pass an object of callback functions to validate all of them. This function will be executed on its own without depending on other validation rules include required attribute.
- **valueAsNumber**
  - Returns a Number normally. If something goes wrong NaN will be returned.
- **valueAsDate**
  - Returns a Date object normally. If something goes wrong null will be returned.
- **setValueAs**
  - Return input value by running through the function.
- **disabled**
  - Set disabled to true will lead input value to be undefined and input control to be disabled.
- **onChange**
  - onChange function event to be invoked in the change event.
- **onBlur**
  - onBlur function event to be invoked in the blur event.
- **value**
  - Set up value for the registered input. This prop should be utilised inside useEffect or invoke once, each re-run will update or overwrite the input value which you have supplied.
- **shouldUnregister**
  - Input will be unregistered after unmount and defaultValues will be removed as well.
- **deps**
  - Validation will be triggered for the dependent inputs,it only limited to register api not trigger.

#### Rules
- nameはユニークである必要がある

#### Tips
forwardRefを使う場合
```tsx
const Select = React.forwardRef(({ onChange, onBlur, name, label }, ref) => (
  <select name={name} ref={ref} onChange={onChange} onBlur={onBlur}>
    <option value="20">20</option>
    <option value="30">30</option>
  </select>
));
```

### handleSubmit
This function will receive the form data if form validation is successful.

validationが成功したらformのdataが返される関数

#### Props
- SubmitHandler
- SubmitErrorHandler

#### examples

```jsx
import React from "react";
import { useForm, SubmitHandler } from "react-hook-form";

type FormValues = {
  firstName: string;
  lastName: string;
  email: string;
};

export default function App() {
  const { register, handleSubmit } = useForm<FormValues>();
  const onSubmit: SubmitHandler<FormValues> = data => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("firstName")} />
      <input {...register("lastName")} />
      <input type="email" {...register("email")} />

      <input type="submit" />
    </form>
  );
}
```

handleSubmit メソッドは、第 1 引数の関数を『バリデーションが成功した場合』に実行します。つまり、バリデーションが失敗した場合には、 onSubmit 関数は実行されないままになります。  

### formState
This object contains information about the entire form state. It helps you to keep on track with the user's interaction with your form application.

このオブジェクトは、フォームの状態全体に関する情報を含んでいます。これは、フォームアプリケーションとユーザーのインタラクションを追跡するのに役立ちます。  
state を参照したい場合は、 useForm フックを使う時に、 formState オブジェクトの値として、対象の state 名を書いておく必要があります。  

