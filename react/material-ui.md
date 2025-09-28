---
title: Material-UI メモ
---


# Material UI

{% raw %}
## sxをbreakpointで分ける
https://mui.com/system/getting-started/usage/#responsive-values
```jsx
<Typography sx={{ mr: 2 }}>
```
これを`md`以上に設定するには、
```jsx
<Typography sx={{ mr: { md: 2 } }}>
```

`xs`と`md`でflex方向を分ける
```jsx
<Box  display="flex"  flexDirection={{ xs: 'column', md: 'row' }} >
```
{% endraw %}


