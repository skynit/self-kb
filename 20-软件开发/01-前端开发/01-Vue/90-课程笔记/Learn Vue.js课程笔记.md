---
title: "Learn Vue.js – Tutorial for Beginners"
source: "https://www.youtube.com/watch?v=Kt2E8nblvXU"
author:
  - "[[freeCodeCamp]]"
  - "[[Rachel Johnson]]"
  - "[[Scrimba]]"
published: 2025
created: 2026-06-01
updated: 2026-06-01
description: "Vue.js 综合入门课程，包含 6 个实战项目：Vue Facts、Grammar's Recipe Book、Animal Shelter、Daily Gratitude Pad、Pokédex、Definition Detective"
tags:
  - "clippings"
  - "vue"
  - "tutorial"
related:
  - "[[20-软件开发/01-前端开发/01-Vue/01-核心概念/Vue3-Overview|Vue3 概述]]"
---

![](https://www.youtube.com/watch?v=Kt2E8nblvXU)

> 课程来源：freeCodeCamp × Scrimba
> 讲师：Rachel Johnson
> 课程版本：Vue 3.5.17

## 课程大纲

### Section 1：Getting Started
- Vue 介绍与优势
- CDN 方式创建第一个 Vue 应用
- 使用 create-vue 安装 Vue
- 项目结构解析

### Section 2：Fundamentals
- 组件（Single File Component）
- 布局组件（Header/Main/Footer 拆分）
- @ 别名（Vite alias）
- 响应式系统与 ref
- 模板语法（文本插值）
- v-bind 属性绑定
- 图片与资源处理（3 种方法）

### 后续章节（未包含在转录中）
- Section 3+：路由、状态管理、API 调用等高级主题

## 视频转录

> 转录方式：yt-dlp 下载英文字幕（auto-generated），人工整理。

Welcome to this comprehensive Vue.js course for beginners. Vue.js is a progressive JavaScript framework known for its simplicity and versatility. In this course, you will learn how to use Vue.js to build fully functional interactive applications. And by the end, you'll be able to create a clean, organized, and production-ready application that utilizes all the fundamental features of Vue.js. Rachel Johnson from Scrimba developed this course.

Welcome to Scrimba's Learn View course. This is a beginner-friendly journey that will help you master the fundamentals of Vue.js. Whether you're new to JavaScript frameworks or looking to add Vue to your toolkit, this course is designed to get you up and running. In this course, you'll learn the basics of Vue, one of the more popular JavaScript frameworks for building user interfaces. Vue is known for its simplicity, flexibility, and performance, making it an excellent choice for both small projects and large scale applications. As we learn about Vue from the ground up, we'll be building six Vue-powered projects that are perfect for practicing your newfound skills. By the end of the course, you'll have a solid foundation in Vue and the confidence to start building your own projects.

We'll start with a simple Vue facts page where we'll learn how to display dynamic data within what's called components. Then we'll create a page from Grammar's recipe book. This simple app lets users click between photos, ingredients, and instructions, all using data from external files. Next up is this animal shelter listing where you'll learn to filter data by type and display dynamic descriptions. Our fourth project is a daily gratitude pad where you'll build a real-time journaling app that timestamps and displays your entries throughout the day. Getting more advanced, we'll create a Pokédex application that pulls data from an external API. The perfect way to learn how Vue handles external data. And for our capstone project, you'll build definition detective, a word guessing game that uses a dictionary API to display real definitions as part of the gameplay.

Now, don't worry if some of these projects sound complex. We'll break everything down into manageable steps, and by the time you get to the project, you will have all the skills that you need.

---

### Section 1: Getting Started with Vue

In section one, we're going to focus on getting started with Vue. We'll start by introducing Vue, what it is, and why we should learn it. You'll learn how to create your first Vue app using a CDN, turning a static web page into a dynamic one. Then we'll install Vue locally using create vue, the official Vue project scaffolding tool, and you'll explore the anatomy of a Vue project.

Now before we go ahead, let's talk about prerequisites. Since this is a Vue course, it is important to have a basic understanding of HTML, CSS, and JavaScript. Vue builds on these foundational technologies, so familiarity with them will help you get the most out of this course.

#### What is Vue?

Let's start with a super important question. What is Vue? Well, Vue is a progressive JavaScript framework that makes building user interfaces and single page applications a breeze. Created by Evan You in 2014, Vue has grown to be one of the more popular front-end frameworks in the world alongside React and Angular. In this course, we'll be focusing on Vue 3, which is the latest major version of the framework. At the time of this recording, the latest stable release version is 3.5.17.

Now, Vue likely would have updated by the time you're learning. So, there might be some slight differences between what you see here and what you encounter in the real world. But don't worry, if the changes are significant, we'll be sure to update your learning experience accordingly.

So, with so many other options out there, why should we learn Vue.js? Well, it isn't just another JavaScript framework, and the numbers really back this up. Stack Overflow's 2024 developer survey shows Vue as the fourth most popular front-end JavaScript framework with an impressive 60.2% admiration rating, which is just 2% below first place. The State of JavaScript 2024 report paints an even more compelling picture with Vue ranking second in usage, awareness, and developer positivity, and third in interest, and retention. And these rankings have either held steady or improved over the last few years.

The open-source community's enthusiasm for Vue is evident in its GitHub statistics with over 28,000 stars and 33,000 forks. But it's not just independent developers who love Vue. Industry giants like Adobe, Nintendo, the Alibaba group along with popular services like Zoom, GitLab, Grammarly, Netflix, and Trust Pilot all use Vue in their production environments. And perhaps the most telling is Vue's consistent growth in adoption. 5 to 6 million weekly downloads on npm with steady year-over-year growth.

#### Vue via CDN

Before we dive into setting up Vue locally or using build tools, we're going to start with something simple, which is using Vue via a CDN. This means we can include Vue in our project with a script tag in our HTML file. This will let us skip the setup process for now and jump straight into making those fingers busy.

To start using Vue, we're going to include the library using a CDN. And to do that, all we have to do is add a script tag in the head of our HTML file and specify the source location just like any other CDN. And the best place to grab the CDN source URL directly is the official Vue documentation.

Now, Vue works by attaching itself to a specific part of the HTML, usually a div, and then controlling what happens inside of that element. So, first we're going to wrap everything in the body tag inside of a div and give it an id of app. We're going to use Vue to change out the world of hello world to a name of our choice that we'll define using Vue. We're going to replace world with curly brackets name and then closing curly brackets. This is Vue's text interpolation syntax. It will tell Vue to render the value of a name variable inside of the H1 tags.

```html
<div id="app">
  <h1>Hello {{ name }}</h1>
</div>

<script>
const { createApp, ref } = Vue;

createApp({
  setup() {
    const name = ref('Rachel');
    return { name };
  }
}).mount('#app');
</script>
```

What's happening here is that I'm grabbing the createApp and ref functions from Vue. With this line, I'm using the createApp function to create a Vue app instance. Everything inside of it defines how the app will behave. Inside the curly brackets, I'm going to start with a setup function. This function gets run the moment that our app loads. And this is where we'll define our data and logic. So let's add some data. Create a constant with the name of name. And here I'm going to type ref('Rachel'). Ref is something that we'll explore later. But for now, think of it as a Vue variable. Then we have to make sure we can access name from the HTML by returning it. So return name and finally mount it to something with an id of app.

When the page loads, Vue takes over this div with an id of app and it replaces this name with the value of name which we set and returned down here in our JavaScript. And just like that, you've learned the very very basics of Vue and how we can extend a standard HTML file.

#### Installing Vue with create-vue

In most real world Vue projects, Vue is installed locally using npm. We're going to install Vue locally using the official Vue project scaffolding tool, create-vue. This handy command line tool does exactly what it says on the tin. It spins up a Vue project right where we want it.

```bash
npm create vue@latest
```

Always include this `@latest` to make sure you're using the most up-to-date version of create-vue. Without it, npm might actually resolve to a cached or outdated version. Under the hood, create-vue leverages Vite, a fast and modern build tool to power both the development server and the production build.

The command line will now ask us a few questions to set up the project. First, it asks for a project name. The remaining prompts are all about customizing the features and tools you want included in your project. Once we've answered all the prompts, create-vue will go ahead and scaffold the Vue project for us. Then follow the instructions:

```bash
npm install    # Install dependencies
npm run dev    # Start development server
```

#### Project Anatomy

Now that we've set up our Vue project using create-vue, let's take a look at the project structure that it generated for us.

- **source/** — Where you'll spend most of your time
  - **assets/** — Project assets (CSS, images, SVGs)
  - **components/** — Vue components live here
  - **App.vue** — The heart of your application (script + template + style)
  - **main.js** — Links HTML to Vue project (createApp and mount logic)
- **index.html** — HTML file served as landing page (div#app where Vue mounts)
- **public/** — Static assets exposed to the public as-is (favicon etc.)
- **Configuration files**
  - `jsconfig.json` — Determines which files get compiled
  - `package.json` / `package-lock.json` — Dependencies and scripts
  - `vite.config.js` — Vite build tool configuration

---

### Section 2: Fundamentals

In this section, we're going to take your Vue skills to the next level. We'll build on what you've learned in the previous section and dive into some powerful Vue features.

#### Vue Components (Single File Component)

In Vue, everything starts with a component. It's a special file format that contains the HTML, CSS, and JavaScript in one neat package. This structure plus this .vue file extension is a special file format that allows us to work on the template, logic, and styling of a Vue component in one file. The classic trio of HTML in the template, CSS in the styles, and JavaScript in the script section.

```vue
<script setup>
import { ref } from 'vue'
const name = ref('Rachel')
const emoji = ref('⚡')
</script>

<template>
  <h1>{{ emoji }} {{ name }}</h1>
</template>

<style scoped>
h1 { color: blue; }
</style>
```

Our core App.vue file is in itself a component. In the HTML CDN version, we had to use createApp and mount to initialize the Vue app, and then wrap everything in the setup function and explicitly return each of the variables. But in the Vue component, all that boilerplate code is handled for us. Here we just have to import ref and then define our variables which can then be used directly in the template section. All we have to do is include this `setup` keyword within the script block. Doing that essentially turns this entire block into the setup function.

**Separation of Concerns**: You might be thinking, "Hang on, I thought we were supposed to keep HTML, CSS, and JavaScript in different files." Vue takes a different approach. Instead of separating by language, it separates by purpose. The component itself is the concern. Each component does one thing. For example, a component renders, styles, and determines the interactivity of a footer. And all the code it needs for that footer, the template, logic, and styles, is kept together. This makes your components easier to read, test, reuse, and update.

#### Layout Components

Layout components or sometimes called structural components can be headers, footers, sidebars, etc. Essentially, the elements that give your app its structure. They don't usually have much complex logic, but they play a big role in how everything fits together.

The process of splitting into components:

1. Create component files (Header.vue, Main.vue, Footer.vue) in components folder
2. Provide the SFC skeleton (script setup, template, style scoped)
3. Move relevant HTML from App.vue to each component's template
4. Move relevant script (refs) to each component's script
5. Move relevant CSS to each component's style scoped
6. Import and use components in App.vue

```vue
<!-- App.vue -->
<script setup>
import Header from '@/components/Header.vue'
import Main from '@/components/Main.vue'
import Footer from '@/components/Footer.vue'
</script>

<template>
  <Header />
  <Main />
  <Footer />
</template>
```

**Naming convention**: Capitalized word + .vue (e.g., `Header.vue`, not `header.vue`).

**Scoped styles**: The `scoped` keyword means everything in the styles block is scoped to only this specific component. This makes it a lot easier to target elements without having to specify a bunch of parents. In a contained component, we can simplify selectors — no need for parent selectors like `footer span`, just `span`.

#### The @ Alias

We used `import Header from '@/components/Header.vue'`. This `@` is an alias given to us by Vite, which is the build tool utilized by create-vue. In `vite.config.js`, this part of the code sets up an alias or a shortcut for our project. Specifically, it tells Vite: whenever you see this `@` symbol in the code, replace it with the absolute file path to the source folder. So the `@` alias takes us back to the source folder from wherever we are at and then we can traverse from there. While `./` (relative path) works too, the `@` alias will come into play later when we're linking and importing assets.

#### Reactivity and Refs

When something is made reactive in Vue, like a variable, Vue watches it. If the value of a reactive variable changes, Vue will automatically rerender parts of the UI as needed. Vue's reactivity system is what makes it feel so live and automatic. You don't need to write code to manually update the DOM.

To create reactive state for a variable, we use the ref function:

```js
import { ref } from 'vue'

const quote = ref('Life is beautiful')
const author = ref('John Johnson')
const year = ref(2025)
```

This variable is now what we call a **ref object**. This is a special object that helps Vue keep track of the reactive states in the app. These are often called **refs**. If you hear me say "the quote ref," you'll know that I'm referring to a variable that has been made reactive using the ref function.

Note: Even though we've only used strings for our refs, we can actually use a whole bunch of types: strings, booleans, numbers, and arrays, just to name a few.

#### Template Syntax (Text Interpolation)

Vue uses an HTML-based syntax that lets us bind data such as refs in our components to certain elements of the DOM. Text interpolation uses the **mustache syntax** `{{ }}`:

```vue
<template>
  <h1>{{ title }}</h1>
  <p>{{ quote }}</p>
</template>
```

**Important**: `title` is technically a ref object, so in JavaScript code we have to specify the `.value` property in order to actually change its value:

```js
title.value = 'Programming Quotes'  // ✅ In script section
```

But in the template, we just write `{{ title }}` — Vue automatically unwraps the `.value` property for us.

#### v-bind (Attribute Binding)

There are cases where the mustache syntax can't be used at all — when we need to dynamically populate an HTML attribute value. For example, we can't use `{{ href }}` inside an `href` attribute.

Vue gives us the `v-bind` directive. It's something we can attach to HTML attributes when we want Vue to take control of them:

```vue
<template>
  <a v-bind:href="href">Link</a>
</template>

<script setup>
import { ref } from 'vue'
const href = ref('https://scrimba.com')
</script>
```

**v-bind works with boolean attributes** too:

```vue
const isButtonDisabled = ref(true)
<button v-bind:disabled="isButtonDisabled">Share</button>
```

**Shorthands**:

```vue
<!-- v-bind: → : -->
<a :href="href">Link</a>

<!-- Same-name shorthand (attribute name matches ref name) -->
<a :href>Link</a>
```

#### Images and Assets in Vue

There are three methods to work with images:

**Method 1: Absolute path to public folder**

Files in the `public/` folder are served as-is, don't go through Vite's bundling process.

```vue
<img src="/images/cat.jpg" alt="cat">
```

**Method 2: Relative path to assets folder**

Use the `@` alias to reference files in `src/assets/`.

```vue
<img src="@/assets/images/cat.jpg" alt="cat">
```

**Method 3: Import asset into component**

Import from the assets folder and bind to source attribute.

```vue
<script setup>
import catImg from '@/assets/images/cat.jpg'
</script>

<template>
  <img :src="catImg" alt="cat">
</template>
```

**Which method to choose?**

| Method | When to use |
|--------|------------|
| Public folder | Large assets, files that shouldn't be processed, assets used by other frameworks |
| Assets folder (@) | Small to medium assets, static images, assets that benefit from Vite's optimization |
| Import into component | Dynamic images, assets used conditionally, images that need to be referenced programmatically |