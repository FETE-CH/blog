---
title: Flex 布局
date: 2018-03-29 16:02:00
tags: 
    - 前端
    - CSS
    - Flex
    - 网页布局
categories: 学习笔记
reward: true
toc: true
---

## 1. Flex 容器

设置显示方式为flex

```css
display: flex | inline-flex;
```
**注意：当时设置 flex 布局之后，子元素的 float、clear、vertical-align 的属性将会失效。**

下面六种属性可以设置在容器上，它们分别是：

- flex-direction
- flex-wrap
- flex-flow
- justify-content
- align-items
- align-content

<!-- more -->

1. flex-direction: 决定主轴的方向(即项目的排列方向)

    ```css
    flex-direction: row | row-reverse | column | column-reverse;
    ```
    - row：默认值,主轴为水平方向，起点在左端。
    - row-reverse：主轴为水平方向，起点在右端 
    - column：主轴为垂直方向，起点在上沿  
    - column-reverse：主轴为垂直方向，起点在下沿

2. flex-wrap: 决定容器内项目是否可换行

    ```css
    flex-wrap: nowrap | wrap | wrap-reverse;
    ```

    - nowrap：默认值，不换行，即当主轴尺寸固定时，当空间不足时，项目尺寸会随之调整而并不会挤到下一行。
    - wrap：项目主轴总尺寸超出容器时换行，第一行在上方
    - wrap-reverse：换行，第一行在下方

3. flex-flow: flex-direction 和 flex-wrap 的简写形式

    ```css
    flex-flow: <flex-direction> || <flex-wrap>;
    ```

4. justify-content：定义了项目在主轴的对齐方式。

    ```css
    justify-content: flex-start | flex-end | center | space-between | space-around;
    ```

    - 默认值: flex-start 左对齐
    - flex-end：右对齐
    - center：居中
    - space-between：两端对齐，项目之间的间隔相等，即剩余空间等分成间隙。
    - space-around：每个项目两侧的间隔相等，所以项目之间的间隔比项目与边缘的间隔大一倍。

5. align-items: 定义了项目在交叉轴上的对齐方式

    ```css
    align-items: flex-start | flex-end | center | baseline | stretch;
    ```

    - 默认值为 stretch 即如果项目未设置高度或者设为 auto，将占满整个容器的高度。假设容器高度设置为 100px，而项目都没有设置高度的情况下，则项目的高度也为 100px。
    - flex-start：交叉轴的起点对齐
    - flex-end：交叉轴的终点对齐
    - center：交叉轴的中点对齐
    - baseline: 项目的第一行文字的基线对齐

6. align-content: 定义了多根轴线的对齐方式，如果项目只有一根轴线，那么该属性将不起作用

    ```css
    align-content: flex-start | flex-end | center | space-between | space-around | stretch;
    ```

    当 flex-wrap 设置为 wrap  的时候，容器可能会出现多条轴线，这时候你就需要去设置多条轴线之间的对齐方式了。

    - 当值为 stretch 时会多条轴线平分容器的垂直方向上的空间。
    - flex-start：轴线全部在交叉轴上的起点对齐
    - flex-end：轴线全部在交叉轴上的终点对齐
    - center：轴线全部在交叉轴上的中间对齐
    - space-between：轴线两端对齐，之间的间隔相等，即剩余空间等分成间隙。
    - space-around：每个轴线两侧的间隔相等，所以轴线之间的间隔比轴线与边缘的间隔大一倍。

## 2. Flex 项目属性（子元素）
有六种属性可运用在 item 项目上：

- order
- flex-basis
- flex-grow
- flex-shrink
- flex
- align-self


1. order: 定义项目在容器中的排列顺序，数值越小，排列越靠前，默认值为 0

    ```css
    order: <integer>;
    ```
    
2.  flex-basis: 定义了在分配多余空间之前，项目占据的主轴空间，浏览器根据这个属性，计算主轴是否有多余空间

    ```css
    flex-basis: <length> | auto;
    ```
    默认值：auto，即项目本来的大小, 这时候 item 的宽高取决于 width 或 height 的值。

3. flex-grow: 定义项目的放大比例

    ```css
    flex-grow: <number>;
    ```
    默认值为 0，即如果存在剩余空间，也不放大。
    如果所有项目的 flex-grow 属性都为 1，则它们将等分剩余空间。(如果有的话)  
    如果一个项目的 flex-grow 属性为 2，其他项目都为 1，则前者占据的剩余空间将比其他项多一倍。

4. flex-shrink: 定义了项目的缩小比例

    ```css
    flex-shrink: <number>;
    ```
    默认值: 1，即如果空间不足，该项目将缩小，负值对该属性无效。

    如果所有项目的 flex-shrink 属性都为 1，当空间不足时，都将等比例缩小。    
    如果一个项目的 flex-shrink 属性为 0，其他项目都为 1，则空间不足时，前者不缩小。

5. flex: flex-grow, flex-shrink 和 flex-basis的简写

    ```css
    flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
    ```
    flex 的默认值是以上三个属性值的组合。假设以上三个属性同样取默认值，则 flex 的默认值是 0 1 auto。

6. align-self: 允许单个项目有与其他项目不一样的对齐方式

    单个项目覆盖 align-items 定义的属性  

    默认值为 auto，表示继承父元素的 align-items 属性，如果没有父元素，则等同于 stretch。
    ```css
    align-self: auto | flex-start | flex-end | center | baseline | stretch;
    ```
    这个跟 align-items 属性时一样的，只不过 align-self 是对单个项目生效的，而 align-items 则是对容器下的所有项目生效的。

---

**欢迎转载，转载请注明出处：[https://newkami.cn/2018/03/29/flex-layout/](https://newkami.cn/2018/03/29/flex-layout/)**
