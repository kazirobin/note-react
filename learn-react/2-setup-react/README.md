# 1. Create a React App with Tailwindcss

- terminal command write

```
npm create vite@latest
```

- now install tailwindcss

```
npm install tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

- now edit **Three** file

### 1.tailwind.config.js

```
content: [
"./index.html",
"./src/**/*.{js,ts,jsx,tsx}"
],
```

### 2.src/index.css

```
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### 3.vite.config.js

```
 server:{open:true}
```

now our basic react app with tailwindcss is created...

**now you can also install react icon and react router dom etc.**

```
npm i react-icons
npm i react-router-dom
```

[Back to Home](../../)

[1. extensions](../extensions/)

2. setup-react

[3. children-props](../3-children-props/)

[4. condition-to-show](../4-condition-to-show/)

[5. data.js](../5-data.js/)

[6. form](../6-form/)
