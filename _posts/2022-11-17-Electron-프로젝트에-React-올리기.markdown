---
layout: post
title:  "Electron 프로젝트에 React 올리기"
author: beomine
date:   2022-11-17 14:01:33 +0900
categories: [Study, Electron]
tags: [electron, typeScript, webpack, react]
---

### **1. Electron 프로젝트 생성**
   TypeScript + Webpack template 일렉트론 프로젝트를 생성한다.  
   ```bash
   yarn create electron-app my-new-app --template=webpack-typescript
   ```
     
### **2. 생성된 tsconfig.json 수정**  
   `"compilerOptions":{}` 섹션에 `"jsx":"react-jsx"` 추가한다.
     
### **3. react dependencies 추가**  

   ``` bash
      yarn add react react-dom
      yarn add --dev @types/react @types/react-dom 
   ```
     
### **4. Integrate React code**  
    src/app.tsx 생성 후 아래 코드 작성

   ```tsx
   import ReactDOM from "react-dom/client";

   const root = ReactDOM.createRoot(document.getElementById('app'))
   const App = () => {
         return <div>Hello from React!</div>
   }

   root.render(<App/>);
   ```
   src/render.ts 파일 맨 하단에 `import './app'` 추가  
 
   ``` ts
   import './index.css';

      console.log('👋 This message is being logged by "renderer.js", included via webpack');

      // Add this to the end of the existing file
   import './app';
   ```