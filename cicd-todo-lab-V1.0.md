# ใบงานการทดลอง CI/CD: To-do List App

## วัตถุประสงค์
เพื่อเรียนรู้และฝึกปฏิบัติการทำ CI/CD โดยใช้แอปพลิเคชัน To-do List อย่างง่าย

## เครื่องมือที่ใช้
- GitHub (Version Control และ CI/CD)
- React (Frontend Framework)
- Jest (Testing Framework)
- Vercel (Hosting และ CD)

## การเตรียมสภาพแวดล้อมก่อนการทดลอง

1. ติดตั้ง Node.js และ npm:
   - ไปที่ https://nodejs.org/ และดาวน์โหลด LTS version
   - ติดตั้งตามขั้นตอนที่แนะนำ
   - ตรวจสอบการติดตั้งโดยเปิด Terminal และพิมพ์:
     ```
     node --version
     npm --version
     ```

2. ติดตั้ง Git:
   - ไปที่ https://git-scm.com/ และดาวน์โหลด Git สำหรับระบบปฏิบัติการของคุณ
   - ติดตั้งตามขั้นตอนที่แนะนำ
   - ตรวจสอบการติดตั้งโดยเปิด Terminal และพิมพ์:
     ```
     git --version
     ```

3. สร้างบัญชี GitHub:
   - ไปที่ https://github.com/ และสร้างบัญชีใหม่หากยังไม่มี

4. ติดตั้ง Visual Studio Code (หรือ code editor ที่คุณถนัด):
   - ไปที่ https://code.visualstudio.com/ และดาวน์โหลด
   - ติดตั้งตามขั้นตอนที่แนะนำ

5. สร้างบัญชี Vercel:
   - ไปที่ https://vercel.com/ และสร้างบัญชีใหม่
   - แนะนำให้ใช้บัญชี GitHub ในการ Sign up เพื่อความสะดวกในการเชื่อมต่อ

## ขั้นตอนการทดลอง

### 1. สร้างโปรเจค React และ Push to GitHub

1. เปิด Terminal และรันคำสั่งต่อไปนี้เพื่อสร้างโปรเจค React:
   ```
   npx create-react-app todo-cicd
   cd todo-cicd
   ```

2. เปิด GitHub และสร้าง repository ใหม่:
   - คลิกที่ "+" ด้านบนขวา และเลือก "New repository"
   - ตั้งชื่อ repository เป็น "todo-cicd"
   - เลือก "Public"
   - คลิก "Create repository"

3. ใน Terminal, รันคำสั่งต่อไปนี้เพื่อ push โค้ดไปยัง GitHub:
   ```
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/yourusername/todo-cicd.git
   git push -u origin main
   ```

### 2. พัฒนา To-do List App

1. เปิดโปรเจคใน Visual Studio Code
2. แก้ไขไฟล์ `src/App.js`:

```jsx
import React, { useState } from 'react';

function App() {
  const [todos, setTodos] = useState([]);
  const [input, setInput] = useState('');

  const addTodo = () => {
    if (input) {
      setTodos([...todos, input]);
      setInput('');
    }
  };

  return (
    <div>
      <h1>To-do List</h1>
      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
        placeholder="Enter a todo"
      />
      <button onClick={addTodo}>Add Todo</button>
      <ul>
        {todos.map((todo, index) => (
          <li key={index}>{todo}</li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```

3. สร้างไฟล์ `src/App.test.js` สำหรับการทดสอบ:

```jsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import App from './App';

test('adds a todo when clicking the Add Todo button', () => {
  render(<App />);
  const input = screen.getByPlaceholderText('Enter a todo');
  const button = screen.getByText('Add Todo');

  fireEvent.change(input, { target: { value: 'New todo item' } });
  fireEvent.click(button);

  expect(screen.getByText('New todo item')).toBeInTheDocument();
});
```

4. รันการทดสอบในเครื่องด้วยคำสั่ง:
   ```
   npm test
   ```

5. Commit และ push การเปลี่ยนแปลง:
   ```
   git add .
   git commit -m "Add basic To-do List functionality and test"
   git push
   ```

### 3. ตั้งค่า CI ด้วย GitHub Actions

1. ใน GitHub repository, ไปที่แท็บ "Actions"
2. คลิก "New workflow"
3. เลือก "Node.js" จากรายการ template
4. แก้ไข workflow file ให้เป็นดังนี้:

```yaml
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Use Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '14.x'
    - run: npm ci
    - run: npm run build --if-present
    - run: npm test
```

5. คลิก "Start commit" และ "Commit new file"

### 4. ตั้งค่า CD ด้วย Vercel

1. ไปที่ https://vercel.com/
2. คลิก "New Project"
3. เลือก GitHub repository "todo-cicd"
4. คลิก "Import"
5. ในหน้า "Configure Project":
   - Framework Preset: เลือก "Create React App"
   - คลิก "Deploy"

### 5. ทดสอบ CI/CD Pipeline

1. สร้าง branch ใหม่ใน Terminal:
   ```
   git checkout -b feature/mark-complete
   ```

2. แก้ไข `src/App.js` เพื่อเพิ่มฟังก์ชัน mark as complete:

```jsx
import React, { useState } from 'react';

function App() {
  const [todos, setTodos] = useState([]);
  const [input, setInput] = useState('');

  const addTodo = () => {
    if (input) {
      setTodos([...todos, { text: input, completed: false }]);
      setInput('');
    }
  };

  const toggleComplete = (index) => {
    const newTodos = [...todos];
    newTodos[index].completed = !newTodos[index].completed;
    setTodos(newTodos);
  };

  return (
    <div>
      <h1>To-do List</h1>
      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
        placeholder="Enter a todo"
      />
      <button onClick={addTodo}>Add Todo</button>
      <ul>
        {todos.map((todo, index) => (
          <li 
            key={index} 
            onClick={() => toggleComplete(index)}
            style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}
          >
            {todo.text}
          </li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```

3. อัปเดตไฟล์ `src/App.test.js`:

```jsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import App from './App';

test('adds a todo when clicking the Add Todo button', () => {
  render(<App />);
  const input = screen.getByPlaceholderText('Enter a todo');
  const button = screen.getByText('Add Todo');

  fireEvent.change(input, { target: { value: 'New todo item' } });
  fireEvent.click(button);

  expect(screen.getByText('New todo item')).toBeInTheDocument();
});

test('marks a todo as complete when clicked', () => {
  render(<App />);
  const input = screen.getByPlaceholderText('Enter a todo');
  const button = screen.getByText('Add Todo');

  fireEvent.change(input, { target: { value: 'New todo item' } });
  fireEvent.click(button);

  const todoItem = screen.getByText('New todo item');
  fireEvent.click(todoItem);

  expect(todoItem).toHaveStyle('text-decoration: line-through');
});
```

4. Commit และ push การเปลี่ยนแปลง:
   ```
   git add .
   git commit -m "Add mark as complete feature"
   git push -u origin feature/mark-complete
   ```

5. สร้าง Pull Request:
   - ไปที่ GitHub repository
   - คลิกที่ "Pull requests" tab
   - คลิก "New pull request"
   - เลือก base: main และ compare: feature/mark-complete
   - คลิก "Create pull request"
   - ใส่รายละเอียดของ Pull Request และคลิก "Create pull request" อีกครั้ง

6. ตรวจสอบ GitHub Actions:
   - ในหน้า Pull Request, จะมีส่วนที่แสดงสถานะของ Checks
   - คลิกที่ "Details" เพื่อดูรายละเอียดของ CI process
   - ตรวจสอบว่าการ build และ test ผ่านหรือไม่

7. Review และ Merge Pull Request:
   - หากทุกอย่างผ่าน, คลิก "Merge pull request"
   - เลือก "Merge pull request" (หรือ options อื่นตามต้องการ)
   - คลิก "Confirm merge"

8. ตรวจสอบการ Deploy อัตโนมัติบน Vercel:
   - ไปที่ Vercel Dashboard
   - เลือกโปรเจค todo-cicd
   - ดูที่ "Deployments" เพื่อตรวจสอบว่ามีการ deploy ล่าสุดหรือไม่
   - คลิกที่ URL ที่ Vercel ให้มาเพื่อดูแอปพลิเคชันที่ deploy แล้ว

## สรุปการทดลอง

1. อธิบายกระบวนการ CI ที่เกิดขึ้น:
   - เมื่อมีการ push หรือสร้าง Pull Request, GitHub Actions จะทำงานโดยอัตโนมัติ
   - GitHub Actions จะ build โปรเจคและรันการทดสอบ
   - ผลลัพธ์ของ CI จะแสดงใน Pull Request

2. อธิบายวิธีการทำ automated testing และประโยชน์ที่ได้รับ:
   - เราใช้ Jest และ React Testing Library ในการเขียน unit tests
   - Tests ช่วยตรวจสอบว่าฟีเจอร์ใหม่ไม่ได้ทำให้ฟีเจอร์เดิมเสียหาย
   - Automated tests ช่วยลดเวลาในการทดสอบด้วยมือ

3. อธิบายกระบวนการ CD ที่เกิดขึ้น:
   - เมื่อ merge Pull Request เข้า main branch, Vercel จะตรวจจับการเปลี่ยนแปลง
   - Vercel จะ build และ deploy แอปพลิเคชันโดยอัตโนมัติ
   - เราสามารถเห็นแอปพลิเคชันเวอร์ชันล่าสุดบน URL ที่ Vercel ให้มา

4. สรุปประโยชน์ของการใช้ CI/CD:
   - ลดความผิดพลาดในการ deploy
   - เพิ่มความเร็วในการส่งมอบซอฟต์แวร์
   - ช่วยให้ทีมสามารถตรวจจับและแก้ไขปัญหาได้เร็วขึ้น
   - สนับสนุนการทำงานร่วมกันในทีม

