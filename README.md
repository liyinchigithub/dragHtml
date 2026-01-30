# 元素Html5 拖拽编辑页面

<img width="300" height="600" alt="image" src="https://github.com/user-attachments/assets/19c39df2-af31-484a-8cba-6d5449f80951" />

<img width="300" height="600" alt="image" src="https://github.com/user-attachments/assets/d2ac4d58-a21e-4888-90a8-50ea3603b949" />


## 功能描述
- 实现页面元素的拖拽功能
- 支持拖拽元素的移动
- 支持拖拽元素的缩放
- 字体大小调整
- 字体颜色调整
- 删除元素

## 核心代码示例（精简）

### 拖拽移动与排序
```js
function enableDrag(containerSelector, itemSelector){
  document.querySelectorAll(containerSelector).forEach(container=>{
    container.addEventListener('dragover',e=>{
      e.preventDefault();
      const dragging = container.querySelector('.dragging');
      if(!dragging) return;
      const after = getAfter(container, itemSelector, e.clientY);
      if(after==null) container.appendChild(dragging);
      else container.insertBefore(dragging, after);
    });
  });
  document.querySelectorAll(itemSelector).forEach(item=>{
    item.addEventListener('dragstart',e=>{ item.classList.add('dragging'); e.dataTransfer.setData('text/plain',''); });
    item.addEventListener('dragend',()=> item.classList.remove('dragging'));
  });
  function getAfter(container, selector, y){
    const els=[...container.querySelectorAll(`${selector}:not(.dragging)`)];
    let closest={offset:-Infinity,element:null};
    els.forEach(child=>{ const b=child.getBoundingClientRect(); const o=y-b.top-b.height/2; if(o<0&&o>closest.offset) closest={offset:o,element:child}; });
    return closest.element;
  }
}
// 用法
enableDrag('.page','.section');
enableDrag('.list','.item');
enableDrag('.grid','.badge');
```

### 拖拽缩放（带手柄）
```css
.resize-handle{
    position:absolute;
    right:6px;
    bottom:6px;
    width:12px;
    height:12px;
    background:#1A73E8;
    border-radius:3px;
    cursor:nwse-resize
    }
```
```js
function makeResizable(el){
  el.style.position = getComputedStyle(el).position==='static' ? 'relative' : getComputedStyle(el).position;
  let h=el.querySelector('.resize-handle'); if(!h){ h=document.createElement('div'); h.className='resize-handle'; el.appendChild(h); }
  let sx,sy,sw,sh;
  const down=e=>{ const r=el.getBoundingClientRect(); sx=e.clientX; sy=e.clientY; sw=r.width; sh=r.height; document.addEventListener('mousemove',move); document.addEventListener('mouseup',up); };
  const move=e=>{ const dx=e.clientX-sx, dy=e.clientY-sy; el.style.width=Math.max(60,sw+dx)+'px'; el.style.height=Math.max(32,sh+dy)+'px'; };
  const up=()=>{ document.removeEventListener('mousemove',move); document.removeEventListener('mouseup',up); };
  h.addEventListener('mousedown',down);
}
// 用法
document.querySelectorAll('.illustration,.card,.badge,.banner,.img-box').forEach(makeResizable);
```

### 字体大小与颜色
```js
const panel=document.querySelector('.style-panel');
const fg=panel.querySelector('.color-fg');
const bg=panel.querySelector('.color-bg');
const size=panel.querySelector('.font-size');
let activeEl=null;
document.body.addEventListener('click',e=>{ const t=e.target.closest('[contenteditable], .text, .label, .desc, .quote, .footer, h1'); if(t){ activeEl=t; } });
fg.addEventListener('input',()=>{ if(activeEl) activeEl.style.color=fg.value; });
bg.addEventListener('input',()=>{ if(activeEl) activeEl.style.backgroundColor=bg.value; });
size.addEventListener('input',()=>{ if(activeEl) activeEl.style.fontSize=Number(size.value)+'px'; });
```

### 删除元素（右上角 ×）
```css
.del-handle{
    position:absolute;
    top:6px;
    right:6px;
    width:18px;
    height:18px;
    line-height:18px;
    text-align:center;
    background:#F44336;
    color:#fff;
    border:none;
    border-radius:50%;
    cursor:pointer
    }
```
```js
function attachDelHandle(el){
  if(getComputedStyle(el).position==='static') el.style.position='relative';
  const btn=document.createElement('button'); btn.className='del-handle'; btn.textContent='×'; el.appendChild(btn);
  btn.addEventListener('click',e=>{ e.stopPropagation(); if(confirm('确定删除该元素吗？')) el.remove(); });
}
document.querySelectorAll('.section,.item,.badge,.card,.illustration,.banner,[contenteditable],.img-box').forEach(attachDelHandle);
```


