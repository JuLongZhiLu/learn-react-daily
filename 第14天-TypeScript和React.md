## 第 14 天：TypeScript + React 实战——用类型武装你的代码

在今天的前端工业界，**TypeScript（TS）已经成为了编写 React 应用的绝对事实标准**。不管是大厂、中大型项目，还是现代开源库，几乎 90% 以上都在使用 TS。

为什么大家都抛弃了纯 JavaScript（JS）转向 TS？

因为纯 JS 过于自由，没有任何约束。比如你写了一个卡片组件：
* 你本意是让别人传一个字符串：`<Card title="新闻" />`。
* 结果新同事不小心传了个数字或对象：`<Card title={{ text: 123 }} />`。
* 你的 JS 代码不会在编译时报错，直到用户打开网页，页面直接“白屏崩溃”，你才猛然醒悟。

而 **TypeScript 就像是你的“全天候代码哨兵”**。在你写代码的瞬间，它就能指出你的拼写错误、类型传错，**把 90% 以上的低级 Bug 直接拦截在你的编辑器里**。

今天，我们来学习如何在 React 中写出类型安全、优雅的 TypeScript 代码。

---

## TS + React 的三大核心玩法

在写 React 的 TSX（TypeScript XML）代码时，我们最常需要定义以下三种类型：

### 1. 给 Props 穿上安全铠甲（定义接口 `interface`）
这是最常用的操作。我们需要使用 `interface`（接口）来规定一个组件可以接收哪些 Props，以及这些 Props 是什么类型。

```tsx
// 1. 声明 Props 的类型规格书
interface CardProps {
  title: string;       // 必须是字符串
  likesCount: number;  // 必须是数字
  isHot?: boolean;     // 💡 带问号 ? 代表该属性是可选的（可传可不传）
}

// 2. 将规格书绑定到组件上
function Card({ title, likesCount, isHot = false }: CardProps) {
  return (
    <div>
      <h3>{title} {isHot && '🔥'}</h3>
      <p>点赞数：{likesCount}</p>
    </div>
  );
}
```

### 2. 给 `useState` 指明类型（使用泛型 `<>`）
对于简单的状态，TS 非常聪明，能自动进行**类型推断**：
```tsx
const [count, setCount] = useState(0); // TS 会自动判定 count 是 number 类型
```

但如果状态的初始值是 `null`，或者是一个复杂的对象/数组，我们就必须使用**泛型 `<>`** 显式告诉 TS 它能装什么数据：

```tsx
interface User {
  name: string;
  email: string;
}

// 💡 告诉 React：这个 State 要么是一个 User 对象，要么是 null，初始值是 null
const [user, setUser] = useState<User | null>(null);
```

### 3. 驯服事件对象（Event）
在第 6 天我们学过获取输入框的值：`e.target.value`。在 TS 中，如果你不给 `e` 标明类型，TS 编译器会生气地报警（`any` 类型警告）。

React 官方已经为我们准备好了标准的事件类型：
* **输入框改变事件**：`React.ChangeEvent<HTMLInputElement>`
* **表单提交事件**：`React.FormEvent`

```tsx
function handleChange(e: React.ChangeEvent<HTMLInputElement>) {
  console.log(e.target.value); // 安全无痛地拿到输入框内容
}
```

---

## 动手练习（TSX 版本）

我们回到 React Playground 网站：

[React Playground](https://playcode.io/react?utm_source=chatgpt.com)

1. 点击右上角或创建项目时，将你的项目模板切换为 **TypeScript (React TSX)** 模板（或者确认你的文件后缀是 `.tsx` 而不是 `.jsx`）。
2. 把下面的代码复制到 `index.tsx`：

```tsx
import React, { useState } from 'react';
import { createRoot } from 'react-dom/client';

// 🛠️ 1. 定义数据结构接口
interface Task {
  id: number;
  title: string;
  isCompleted: boolean;
}

// 🛠️ 2. 定义子组件的 Props 规格书
interface TaskItemProps {
  task: Task; // 必须是一个 Task 类型的对象
  onToggle: (id: number) => void; // 必须是一个接收 id 并返回无值(void)的函数
}

// 3. 子组件：展示单个任务（严格受到 TaskItemProps 的约束）
function TaskItem({ task, onToggle }: TaskItemProps) {
  return (
    <div style={{
      display: 'flex',
      justifyContent: 'space-between',
      alignItems: 'center',
      padding: '10px',
      backgroundColor: '#f8fafc',
      borderRadius: '6px',
      marginBottom: '8px',
      border: '1px solid #e2e8f0',
      opacity: task.isCompleted ? 0.6 : 1
    }}>
      <span style={{ textDecoration: task.isCompleted ? 'line-through' : 'none' }}>
        {task.title}
      </span>
      <button 
        style={{
          backgroundColor: task.isCompleted ? '#cbd5e1' : '#6366f1',
          color: 'white',
          border: 'none',
          padding: '4px 8px',
          borderRadius: '4px',
          cursor: 'pointer'
        }}
        onClick={() => onToggle(task.id)}
      >
        {task.isCompleted ? '重做' : '完成'}
      </button>
    </div>
  );
}

// 4. 主组件
function App() {
  // 💡 使用泛型告诉 state：这是一个 Task 类型的数组
  const [tasks, setTasks] = useState<Task[]>([
    { id: 1, title: '学会 TypeScript 接口配置', isCompleted: true },
    { id: 2, title: '掌握 useState 泛型约束', isCompleted: false }
  ]);

  const [inputVal, setInputVal] = useState<string>('');

  // 💡 给事件对象标明严格的 HTML 输入框改变事件类型
  const handleInputChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setInputVal(e.target.value);
  };

  // 💡 给表单提交事件标明类型
  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (inputVal.trim() === '') return;

    const newTask: Task = {
      id: Date.now(),
      title: inputVal,
      isCompleted: false
    };

    setTasks([...tasks, newTask]);
    setInputVal('');
  };

  const handleToggle = (id: number): void => {
    setTasks(tasks.map(t => t.id === id ? { ...t, isCompleted: !t.isCompleted } : t));
  };

  return (
    <div style={{ padding: '24px', maxWidth: '400px', margin: '30px auto', border: '1px solid #cbd5e1', borderRadius: '16px', fontFamily: 'sans-serif' }}>
      <h2>🛡️ TypeScript 任务板</h2>
      <p style={{ fontSize: '13px', color: '#64748b', marginBottom: '20px' }}>
        在 TSX 的严密类型监控下，你传错任何一个变量、甚至拼错字母，代码都无法打包编译！
      </p>

      <form onSubmit={handleSubmit} style={{ display: 'flex', marginBottom: '16px' }}>
        <input 
          style={{ flex: 1, padding: '8px', borderRadius: '6px 0 0 6px', border: '1px solid #cbd5e1' }}
          type="text" 
          value={inputVal}
          onChange={handleInputChange} 
          placeholder="添加新任务..."
        />
        <button style={{ backgroundColor: '#10b981', color: 'white', border: 'none', padding: '0 16px', borderRadius: '0 6px 6px 0', cursor: 'pointer' }} type="submit">
          添加
        </button>
      </form>

      <div>
        {tasks.map(task => (
          // 💡 TaskItem 组件受到严格的类型约束，你无法乱传 props
          <TaskItem 
            key={task.id} 
            task={task} 
            onToggle={handleToggle} 
          />
        ))}
      </div>
    </div>
  );
}

const root = createRoot(document.getElementById('root') as HTMLElement);
root.render(<App />);
```

尝试在右侧 Preview 窗口中：
1. 测试任务板的交互（添加任务、切换完成状态）。
2. **试着在代码里搞点小破坏**：比如把 `<TaskItem task={task} onToggle={handleToggle} />` 改成 `onToggle="哈哈"`，或者拼错成 `onTogglee={handleToggle}`。
3. 你会发现，右侧的编译工具会瞬间弹出刺眼的红色警报，并精准指出哪一行哪一个变量类型传错了。这就是 TypeScript 给予你的**绝对安全感**。

---

## 明天可以学什么？

恭喜你！今天你写出了人生中第一行高规格的 **TypeScript + React (TSX)** 安全代码。这是迈向现代前端高级工程师、通往大厂技术标准的极为关键的一步！

到现在为止，我们已经完成了整整 14 天的系统学习。明天，是我们的最后一课：**第 15 天：React 项目结构与工业级规范（如何写出大厂般优雅的工程项目？）**
