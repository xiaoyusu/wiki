## #WDG API

<div class="bi-table">
  <table>
    <colgroup>
      <col width="90px" />
      <col width="90px" />
    </colgroup>
    <tbody>
      <tr>
        <td rowspan="1" colSpan="1">
          <div data-type="p">API</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">说明</div>
        </td>
      </tr>
      <tr>
        <td rowspan="1" colSpan="1">
          <div data-type="p">WDG.start(timeout)</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p"><span data-type="color" style="color:rgb(245, 34, 45)">功能：</span>启动看门狗定时器</div>
          <div data-type="p"><span data-type="color" style="color:rgb(245, 34, 45)">参数：</span> timeout：喂狗超时时间，单位是ms</div>
          <div data-type="p"><span data-type="color" style="color:rgb(245, 34, 45)">返回值：</span>0=ok other=fail</div>
        </td>
      </tr>
      <tr>
        <td rowspan="1" colSpan="1">
          <div data-type="p">WDG.stop()</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p"><span data-type="color" style="color:rgb(245, 34, 45)">功能：</span>停止看门狗定时器</div>
          <div data-type="p"><span data-type="color" style="color:rgb(245, 34, 45)">参数：</span> 无</div>
          <div data-type="p"><span data-type="color" style="color:rgb(245, 34, 45)">返回值：</span>0=ok other=fail</div>
          <div data-type="p"></div>
        </td>
      </tr>
      <tr>
        <td rowspan="1" colSpan="1">
          <div data-type="p">WDG.feed()</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p"><span data-type="color" style="color:rgb(245, 34, 45)">功能：</span>喂狗</div>
          <div data-type="p"><span data-type="color" style="color:rgb(245, 34, 45)">参数：</span> 无</div>
          <div data-type="p"><span data-type="color" style="color:rgb(245, 34, 45)">返回值：</span>0=ok other=fail</div>
          <div data-type="p"></div>
        </td>
      </tr>
    </tbody>
  </table>
</div>



## #看门狗运用
```javascript
WDG.start(2000);
setInterval(function() {
	WDG.feed();
	console.log('feed the dog!\n');
}, 1000);
```

如果在规定的时间内没有进行喂狗操作，系统将自动重启。

## #TODO



 