=== 开始 ===
天气预报网页应用 · 技术文档
项目名称：晴雨 · 天气生活
项目类型：Web 前端应用
技术栈：HTML5 + CSS3 + JavaScript ES6+
版本：v1.0
日期：2026年7月
一、项目概述
本项目是一个功能完整的天气预报网页应用，使用纯前端技术构建，为用户提供直观、美观、智能的天气信息服务。
1.1 核心功能
功能模块	功能描述
城市搜索	输入城市名称查询天气，支持回车键快捷搜索
实时天气	温度、天气状况、湿度、风速、能见度
7天预报	含今天，显示高/低温度和天气状况
地理定位	浏览器自动获取当前位置天气
数据持久化	localStorage 保存最近搜索城市
天气预警	蓝/黄/橙/红标准化预警
详细指标	紫外线、气压、能见度、空气质量、降水概率、放晴时间、日出日落
出门建议	根据天气/温度/预警/紫外线推荐携带物品
生活规划	全天6个时段活动建议
动态背景	晴天云朵动画 / 雨天雨滴动画
二、技术架构
层级	技术	用途
结构层	HTML5	语义化标签搭建页面骨架
样式层	CSS3	毛玻璃效果、响应式布局、动态动画
逻辑层	JavaScript ES6+	数据管理、DOM操作、事件处理
存储层	localStorage	持久化最近搜索城市
三、HTML 结构
text
.weather-bg (动态背景层)  └── .weather-particles (云朵/雨滴容器).weather-app (主卡片)  ├── .search-section (搜索栏)  ├── #alertBanner (预警横幅)  ├── #currentWeather (当前天气)  ├── .gear-suggestion (出门装备建议)  ├── .detail-grid (详细指标 × 8)  ├── .plan-section (今日生活规划 × 6)  ├── #forecastContainer (7天预报)  └── .action-bar (反馈/分享按钮)
四、CSS 样式要点
4.1 毛玻璃效果
使用 backdrop-filter 对元素背后的内容进行高斯模糊，配合半透明背景和边框，产生"毛玻璃"质感。
text
.weather-app {  background: rgba(255, 255, 255, 0.65);  backdrop-filter: blur(18px) saturate(180%);  -webkit-backdrop-filter: blur(18px) saturate(180%);  border: 1px solid rgba(255, 255, 255, 0.5);  box-shadow: 0 30px 50px -12px rgba(0, 20, 40, 0.3);}
4.2 动态主题
通过JS动态切换 body 的类名，实现背景色随天气和预警等级变化。
主题类名	适用场景
.theme-sunny	晴天
.theme-rainy	雨天
.theme-cloudy	多云/阴天
.theme-hot	高温 ≥30°C
.theme-cold	寒冷 ≤5°C
.theme-alert-blue/yellow/orange/red	对应预警等级
4.3 粒子动画
云朵浮动（晴天）：云朵从左侧缓慢飘向右侧，循环播放。
雨滴下落（雨天）：雨滴从屏幕顶部下落到底部，带有旋转和透明度变化。
4.4 响应式断点
断点	布局调整
≥ 700px	完整布局，7列预报网格
600-699px	4列预报网格，适度缩小
420-599px	3列网格，垂直排列
< 420px	2列网格，规划单列
五、JavaScript 核心逻辑
5.1 数据流
用户操作 → updateWeather() → fetchWeather() → generateMockData() → renderAll() → DOM更新 + localStorage存储
5.2 核心函数
函数	职责
generateMockData(city)	生成完整模拟天气数据
generateGearSuggestion()	综合判断生成装备建议（最多3条）
generateDailyPlan()	生成全天6时段生活规划
renderAll(data, isFromStorage)	统一渲染所有DOM元素
updateDynamicBackground()	根据天气切换晴天/雨天动态背景
setThemeByWeather()	切换body主题类名
5.3 数据模型
text
{  city, temp, condition, humidity, windSpeed, visibility,  uv, pressure, airQuality, rainProb, clearTime,  sunrise, sunset, alert, suggestion,  forecast: [{ day, condition, high, low }],  plan: [{ time, activity, desc, icon }]}
5.4 数据持久化

存储键名：weather_last_city


存储时机：每次成功查询天气后


恢复时机：页面初始化时


UI反馈：显示"最近"标签区分缓存数据

text
// 保存localStorage.setItem('weather_last_city', city);// 恢复const saved = localStorage.getItem('weather_last_city');if (saved) updateWeather(saved, false, true);
5.5 事件绑定
事件源	事件	处理逻辑
搜索按钮	click	获取输入值，调用 updateWeather()
输入框	keydown(Enter)	触发搜索
定位按钮	click	调用 Geolocation API
反馈按钮	click	弹出反馈输入框
分享按钮	click	复制或系统分享天气信息
六、动态背景系统
text
晴天模式: 5朵云横向飘动 (CSS animation)雨天模式: 120滴雨下落 (JS生成 + setInterval补充)切换逻辑:  if (condition包含"雨/雷阵雨/雪") → 雨天  else if (temp >= 28) → 高温晴天  else → 普通晴天/多云
七、API 对接方案
7.1 当前状态
项目目前使用模拟数据，便于演示和开发。生产环境可替换为真实天气API。
7.2 推荐 API
API提供商	特点
OpenWeatherMap	全球覆盖，数据准确
和风天气	中文支持好，免费版够用
心知天气	免费版无需Key
7.3 对接示例
text
// 替换 fetchWeather 函数async function fetchWeather(city) {  const key = 'YOUR_API_KEY';  const url = `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${key}&units=metric&lang=zh_cn`;  const res = await fetch(url);  const data = await res.json();  return transformData(data);}
八、浏览器兼容性
特性	Chrome	Firefox	Safari	Edge
backdrop-filter	76+	103+	9+	79+
CSS Grid	57+	52+	10.1+	16+
async/await	55+	52+	10.1+	15+
localStorage	4+	3.5+	4+	12+
Geolocation	5+	3.5+	5+	12+
建议使用 Chrome 76+ 或 Safari 9+ 获得最佳体验。
九、技术亮点总结
1.
毛玻璃设计 - backdrop-filter 营造现代质感
2.
3.
动态粒子系统 - 云朵/雨滴根据天气自动切换
4.
5.
智能规划引擎 - 综合多因素生成全天生活建议
6.
7.
装备推荐 - 预警优先，多条件加权判断
8.
9.
数据持久化 - localStorage 缓存最近城市
10.
11.
响应式布局 - 适配手机/平板/桌面
12.
十、文件清单
text
weather-app/└── index.html    # 完整应用（所有代码内联）
文档结束
=== 结束 ===
