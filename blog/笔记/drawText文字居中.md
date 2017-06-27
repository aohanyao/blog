 	//绘制文字
        mTextRect = new Rect();
        mStr = "我是测试文字";
        //1.测量文字
        mPaint.getTextBounds(mStr, 0, mStr.length(), mTextRect);
        //2.绘制文字
        canvas.drawText(mStr,
                mWidth / 2 - mTextRect.width() / 2,
                mHeight / 2 + mTextRect.height() / 2,
                mPaint);