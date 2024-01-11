```ad-note
**Both the Shallow and Retained size columns represent data in bytes.**
```

## Shallow size

对象自身所占用的内存大小
> This is the size of memory that is held by the object itself.

### Renderer memory

Renderer memory is all memory of the process where an inspected page is rendered: native memory + JS heap memory of the page + JS heap memory of all dedicated workers started by the page.

## Retained size

对象本身占用的内存大小加上其依赖的对象（当对象本身被释放时，会被GC回收的那些依赖对象）的内存大小
> This is the size of memory that is freed once the object itself is deleted along with its dependent objects that were made unreachable from **GC roots**.


## Promise GC Test

<iframe width="80%" height="600px" src="https://stackblitz.com/edit/web-platform-dgb9vl?embed=1&file=index.html&view=editor"/>

## Task Manager 工具

### Memory and JavaScript Memory
[![pi1BUMt.png](https://z1.ax1x.com/2023/11/08/pi1BUMt.png)](https://developer.chrome.com/docs/devtools/memory-problems/#monitor_memory_use_in_realtime_with_the_chrome_task_manager)

### Nodes highlighted yellow have direct references to them from the JavaScript code
[![pi39BvD.png](https://z1.ax1x.com/2023/11/09/pi39BvD.png)](https://developer.chrome.com/docs/devtools/memory-problems/#discover_detached_dom_tree_memory_leaks_with_heap_snapshots)

