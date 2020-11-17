---
title: TextView 添加下划线，删除线，加粗
date: 2020-03-02 21:22:00
categories: 
 - Android
tags: 
 - TextView
---

通过 TextView 的 `setPaintFlags` 方式来添加下划线，删除线，加粗，在原有的 PaintFlags 上添加新的 Flag 而不清除原有的 Flags

* Paint.ANTI_ALIAS_FLAG 抗锯齿

* Paint.FILTER_BITMAP_FLAG 用于对 Bitmap 进行转换（例如缩放）时对 Bitmap 进行双线性过滤

* Paint.DITHER_FLAG  会影响对比设备精度更高的颜色进行下采样的方式

* Paint.UNDERLINE_TEXT_FLAG 下划线

* Paint.STRIKE_THRU_TEXT_FLAG 删除线

* Paint.FAKE_BOLD_TEXT_FLAG 伪粗体，并不是通过选用更高 weight 的字体让文字变粗，而是通过程序在运行时把文字给「描粗」

* Paint.LINEAR_TEXT_FLAG 会调整文本绘制操作以平滑处理缩放比例，应当与 Paint.SUBPIXEL_TEXT_FLAG 一起使用

* Paint.SUBPIXEL_TEXT_FLAG 亚像素级的抗锯齿

```java
public class TextViewUtils {

    public static void addUnderLine(TextView textView) {
        addPaintFlag(textView, Paint.UNDERLINE_TEXT_FLAG);
    }

    public static void removeUnderLine(TextView textView) {
        removePaintFlag(textView, Paint.UNDERLINE_TEXT_FLAG);
    }

    public static void addStrikeThrough(TextView textView) {
        addPaintFlag(textView, Paint.STRIKE_THRU_TEXT_FLAG);
    }

    public static void removeStrikeThrough(TextView textView) {
        removePaintFlag(textView, Paint.STRIKE_THRU_TEXT_FLAG);
    }

    public static void setFakeBold(TextView textView) {
        addPaintFlag(textView, Paint.FAKE_BOLD_TEXT_FLAG);
    }

    public static void removeFakeBold(TextView textView) {
        removePaintFlag(textView, Paint.FAKE_BOLD_TEXT_FLAG);
    }

    private static void addPaintFlag(TextView textView, int paintFlag) {
        textView.setPaintFlags(textView.getPaintFlags() | paintFlag);
    }

    private static void removePaintFlag(TextView textView, int paintFlag) {
        textView.setPaintFlags(textView.getPaintFlags() & (~paintFlag));
    }
}
```

