# vue-circle-progress-ui

一个小组件，基于VUE的UI组件<br> 

圆形进度条<br> 

content: 内部显示内容 Object required<br>
>eq: { type: 'img', url: '' }<br>
>    { type: 'text', text: '' }<br>
>    { type: 'icon', before: '', running: '' } => （基于font awesome）<br>

type: 展示形式 String required<br>
> 'static' => 静态展示由外部控制展示<br>
> 'native' => 动态展示当传入duration不为0时自动进行倒计时展示<br>
progress: 进度条 Int（0 - 100，静态展示中必须传入）<br>
duration: 运行时长 Int（动态展示中必须传入）<br><br>

size: 图形大小 Int （单位为px，默认为40px）<br>
color: 背景颜色 String （单位为十六进制，默认为#0096ff）<br>

注意：在动态展示形式中duration变化会触发重新倒计时<br>
注意：旋转时长为0.1s，可自行在transition中修改<br>