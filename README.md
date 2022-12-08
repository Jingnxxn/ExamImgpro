### 기말 코딩 시험
----------------
## 문제1번

구현결과 :
![image](https://user-images.githubusercontent.com/96435960/206423206-53b9a688-2291-4eb7-8128-cbe8198f3f1d.png)
---------------
코드 :
import cv2 as cv

def setLabel(image, str, contour):
    (text_width, text_height), baseline = cv.getTextSize(str, cv.FONT_HERSHEY_SIMPLEX, 0.7, 1)
    x,y,width,height = cv.boundingRect(contour)
    pt_x = x+int((width-text_width)/2)
    pt_y = y+int((height + text_height)/2)
    cv.rectangle(image, (pt_x, pt_y+baseline), (pt_x+text_width, pt_y-text_height), (200,200,200), cv.FILLED)
    cv.putText(image, str, (pt_x, pt_y), cv.FONT_HERSHEY_SIMPLEX, 0.7, (0,0,0), 1, 8)


img_color = cv.imread('Examimage.png', cv.IMREAD_COLOR)
img_gray = cv.cvtColor(img_color, cv.COLOR_BGR2GRAY)
ret,img_binary = cv.threshold(img_gray, 127, 255, cv.THRESH_BINARY)

contours, hierarchy = cv.findContours(img_binary, cv.RETR_EXTERNAL, cv.CHAIN_APPROX_SIMPLE)

sw = 0

for cnt in contours:
    size = len(cnt)
    print(size)

    epsilon = 0.005 * cv.arcLength(cnt, True)
    approx = cv.approxPolyDP(cnt, epsilon, True)

    size = len(approx)
    print(size)

    if sw % 3 == 0:
        sw = sw + 1
        cv.line(img_color, tuple(approx[0][0]), tuple(approx[size-1][0]), (255, 0, 0), 16)
        for k in range(size-1):
            cv.line(img_color, tuple(approx[k][0]), tuple(approx[k+1][0]), (255, 0, 0), 16)
    elif sw % 3 == 1:
        sw = sw + 1
        cv.line(img_color, tuple(approx[0][0]), tuple(approx[size-1][0]), (0, 255, 0), 16)
        for k in range(size-1):
            cv.line(img_color, tuple(approx[k][0]), tuple(approx[k+1][0]), (0, 255, 0), 16)
    elif sw % 3 == 2:
        sw = sw + 1
        cv.line(img_color, tuple(approx[0][0]), tuple(approx[size-1][0]), (0, 0, 255), 16)
        for k in range(size-1):
            cv.line(img_color, tuple(approx[k][0]), tuple(approx[k+1][0]), (0, 0, 255), 16)


    if cv.isContourConvex(approx):
        if size == 3:
            setLabel(img_color, "triangle", cnt)
        elif size == 4:
            setLabel(img_color, "rectangle", cnt)
        elif size == 5:
            setLabel(img_color, "pentagon", cnt)
        elif size == 6:
            setLabel(img_color, "hexagon", cnt)
        elif size == 8:
            setLabel(img_color, "octagon", cnt)
        elif size == 10:
            setLabel(img_color, "decagon", cnt)
        else:
            setLabel(img_color, "circle", cnt)
    else:
        setLabel(img_color, str(size), cnt)

cv.imshow('result', img_color)
cv.waitKey(0)
