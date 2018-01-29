---
title: js当天、当周、当日
date: 2017-12-04 10:59:04
tags: JavaScript
categories: JavaScript
---
```
<script>
  let d = new Date();
  // 年月日周
  let year = d.getFullYear();
  let month = d.getMonth();
  let date = d.getDate();
  let day = d.getDay();
  // 0 当天，1 当周 2 当月
  let type = 1, start = 0, end = 0;
  
  switch (type) {
    case 0:
      start = new Date(`${year}/${month}/${date} 00:00:00`).getTime() / 1000;
      end = new Date(`${year}/${month}/${date} 23:59:59`).getTime() / 1000;
      break;
    case 1:
      start = new Date(`${year}/${month}/${date - day} 00:00:00`).getTime() / 1000;
      end = new Date(`${year}/${month}/${date + (6 - day)} 23:59:59`).getTime() / 1000;
      break;
    case 2:
      start = new Date(`${year}/${month}/1 00:00:00`).getTime() / 1000;
      let next_month = 0;
      if (month == 11) {
        next_month = new Date(`${year + 1}/${1}/1 00:00:00`).getTime() / 1000;
      } else {
        next_month = new Date(`${year}/${month+2}/1 00:00:00`).getTime() / 1000;
      }
      end = next_month  - 1
      break;
  }
  console.log(start, end);
</script>
```