> js 执行是单线程，浏览器和 Nodejs 的 V8 内核都是如此，最近有一些新技术允许使用多线程来克服

## 纯前端方案 - Web Worker

```js
// 例子
worker = new Worker("./a.js");
worker.postMessage();
// a.js
this.onmessage(() => {});
this.postMessage();
```

> 注意：
>
> - 子线程不能操作 DOM、BOM
> - 子线程不能嵌套子线程
> - 不能跨域
> - 不能共享数据，而是重新复制一份（深度克隆）

## Nodejs 方案 - Worker Threads

> https://nodejs.org/api/worker_threads.html

- 适用：计算量大的多个并行任务/安全性不明确的任务
- 实践：服务端数据处理
  - 配合 Promise.all
  - 从 ES 读取数据后，进行批量映射
- 框架推荐：microjob
- 注意
  - 依赖的数据需要手动传入，不能共享数据
  - 有些服务器会有线程数限制 maxWorkers

```javascript
// 例子
// execute function in api param
const func_exec = async (
  dataArr: any[],
  [func_str]: [string]
): Promise<string> => {
  // worker threads pool: safety
  const res = await job(
    ({dataArr, func_str}) => {
      // 创建Function
      const func = new Function(`return ${func_str}`)();
      // 执行
      return func.apply(null, dataArr);
    },
    {
      data: {dataArr, func_str}
    }
  );
  return res;
};
```
