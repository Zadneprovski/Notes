# Примеры

>цитата
---
- First list item 
- Second list item
- Third list item
* * *
- [x] This is a completed task. 
- [ ] This is an incomplete task.
---
1. First list item 
	1. Ordered nested list item 
2. Second list item - Unordered nested list item
---
# Оформление кода
*Блок кода - три одинарные кавычки (буква ё на клавиатуре)*
```
cd ~/Desktop
```

*Подсветка синтаксиса, если добавить код языка после первых кавычек*
```js 
function fancyAlert(arg) { 
	if(arg) { 
		$.facebox({div:'#foo'}) } 
	}
```
[[Коды языков для markdown]]

---
# Ссылки

`[[Название заметки]]`

**Чтобы текст ссылки отличался от названия заметки, вы можете использовать символ `|`:**

`[[Название заметки|Текст ссылки]]`

# Формулы

Пишутся на **[[LaTex]]**

$$ a^2 + \frac{a}{b}$$