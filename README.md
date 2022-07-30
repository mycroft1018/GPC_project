# GPC_project
# %%
# 读取html文件
import os
import shutil  # 导入模块
content_html = []
path = 'D:\\GP\\2012\\'  # 指定路径
dirs = os.listdir(path)  # 遍历指定路径下的所有文件
accum=0
for i in dirs:
    file = open(path + i, encoding='utf-8')
    content_html_every = file.read()
    content_html.append(content_html_every)
    accum=accum+1
    print(accum)
    file.close()

crawl_number = accum
def repair(order,lists):
    for x in range(crawl_number):
        if x not in order:
            lists.insert(x,".")
    print(len(lists))

# %%

# 提取项目名称
title = []
case_order_title = []
title_every = ""
for i in content_html[:crawl_number]:
    title_every = i[i.index('<title>') + len('<title>'):i.index('</title>')]
    if content_html.index(i) not in case_order_title and title_every != "":
        case_order_title.append(content_html.index(i))
        title.append(title_every)
        print(title_every)
        print(content_html.index(i))

print(len(title))


# %%
# 提取项目状态
result = []
case_order_result = []
result_every = ""
for i in content_html[:crawl_number]:
    if "失败" in i or "终止" in i or "废标" in i or "流标" in i or "不足三家" in i:
        result_every = "失败"
    else:
        result_every = "成交"
    if content_html.index(i) not in case_order_result and result_every != "":
        case_order_result.append(content_html.index(i))
        result.append(result_every)
        print(result_every)
        print(content_html.index(i))

print(len(result))
# %%
# 提取采购方式
method = []
case_order_method = []
method_every = ""
method_name = ["公开招标", "竞争性谈判", "竞争性磋商", "邀请招标", "询价", "单一来源", "协议供货", "定点采购", "谈判", "网上竞价", "公开", "邀请", "竞价采购"]
for i in content_html[:crawl_number]:
    for method_symbol in method_name:
        if method_symbol in i:
            method_every = method_symbol
            if content_html.index(i) not in case_order_method and method_every != "":
                case_order_method.append(content_html.index(i))
                method.append(method_every)
                print(method_every)
                print(content_html.index(i))
            break
    if content_html.index(i) not in case_order_method:
        method_every = method_symbol = "未注明"
        if content_html.index(i) not in case_order_method and method_every != "":
            case_order_method.append(content_html.index(i))
            method.append(method_every)
            print(method_every)
            print(content_html.index(i))

print(len(method))

# %%
#成交公告公示日期，详细到分秒
import re
# b= re.compile(r'^.*年.*月.*日')
# a="DAFA1FJ你哈飞洒发2017年5月9日"
# m1 = b.search('DAFA1FJ你哈飞洒发2017年5月9日')
# m2 = b.findall('DAFA1FJ你哈飞洒发2017年5月9日')
date_deal_every= ""
a=1
#num = re.sub(r'#.*$', "", phone)
date_deal = []
case_order_deal = []
regex_list = [
    # 2013年8月15日 22:46:21
    "(\d{4}.\d{1,2}.\d{1,2} \d{1,2}:\d{1,2}:\d{1,2})",
    # "2013年8月15日 22:46"
    # "(\d{4}-\d{1,2}-\d{1,2} \d{1,2}:\d{1,2})",
    # # "2014年5月11日"
    # "(\d{4}-\d{1,2}-\d{1,2})",
    # # "2014年5月"
    # "(\d{4}-\d{1,2})",
]
for i in content_html[:crawl_number]:

 for regex in regex_list:
     date_deal_every = re.search(regex, i)
 if date_deal_every:
     date_deal_every = date_deal_every.group(1)
     print(date_deal_every)
     a+=1
     case_order_deal.append(content_html.index(i))
     date_deal.append(date_deal_every)
     # method.append(method_every)
     continue
 else:
    print("没有获取到有效日期")
repair(case_order_deal,date_deal)


# %%
##成交公告公发布日期
import re
from bs4 import BeautifulSoup
# b= re.compile(r'^.*年.*月.*日')
# a="DAFA1FJ你哈飞洒发2017年5月9日"
# m1 = b.search('DAFA1FJ你哈飞洒发2017年5月9日')
# m2 = b.findall('DAFA1FJ你哈飞洒发2017年5月9日')

def type1(x):
    flag = 0
    regex_list = ["(\d{4}年\d{1,2}月\d{1,2}日)",
    "(\d{4}年\d{1,2}月\d{1,2} 日)",
"(\d{4}年\d{1,2} 月\d{1,2}日)",
"(\d{4} 年\d{1,2}月\d{1,2}日)",
"(\d{4} 年\d{1,2} 月\d{1,2} 日)",
    "(\d{4}-\d{1,2}-\d{1,2})",
    "(\d{4}-\d{1,2}-\d{1,2} )"]

    for regex in regex_list:
        date_public_every = re.search(regex,x)
        if date_public_every:
            date_public_every = date_public_every.group(1)
            print(date_public_every)
            return date_public_every
        else:
            flag+=1
            print(flag)
        if flag == 7:
            date_public_every="."
            return date_public_every

def type2(x):
    regex_list = ["(\d{4}年\d{1,2}月\d{1,2}日)",
                  "(\d{4}年\d{1,2}月\d{1,2} 日)",
                  "(\d{4}年\d{1,2} 月\d{1,2}日)",
                  "(\d{4} 年\d{1,2}月\d{1,2}日)",
                  "(\d{4} 年\d{1,2} 月\d{1,2} 日)",
                  "(\d{4}-\d{1,2}-\d{1,2})",
                  "(\d{4}-\d{1,2}-\d{1,2} )"]
    x= x.replace(" ", "")
    flag = 0
    for regex in regex_list:

        date_public_every = re.search(regex,x)
        if date_public_every:
            date_public_every = date_public_every.group(1)
            print(date_public_every)
            return date_public_every
        else:
            flag+=1
            print(flag)
        if  flag==7:
            date_public_every = "."
            return date_public_every

def type3(x):
    keywords = ["二零一二", "二零一一", "二零一零", "二零零九", "二零零八", "二零零七", "二零零六", "二零零五", "二零零四", "二零零三", "二零零二", "二零零一", "二零一三",
                " 二O一二", " 二O一一", " 二O一O", " 二OO九", "二OO八", "二OO七", "二OO六", "二OO五", "二OO四", "二OO三", "二OO二", "二OO一",
                "二0一二", " 二0一一", " 二0一0", " 二00九", "二00八", "二00七", "二00六", "二00五", "二00四", "二00三", "二00二", "二00一",
                ]
    flag = 0
    for symbol in keywords:
        date_public_every=""
        if symbol in x:  # and len(i[:pos + len(symbol)]) >= 20
            pos = x.find(symbol)
            date_public_every = x[pos:]
            date_public_every = date_public_every[:date_public_every.index("日") + 1]
            if len(date_public_every) >= 9:
                print(date_public_every)
                return date_public_every
        else:
           flag+=1
           print(flag)
        if flag==37:
            date_public_every = "."
            return date_public_every


date_public_every= ""
date_public = []
case_order_public = []

for i in content_html[:crawl_number]:
    if type1(i)!=".":
        case_order_public.append(content_html.index(i))
        date_public.append(type1(i))
    else:
        if type3(i)!= ".":
            case_order_public.append(content_html.index(i))
            date_public.append(type3(i))
        else:
            if type2(i) != ".":
                case_order_public.append(content_html.index(i))
                date_public.append(type2(i))

repair(case_order_public,date_public)


#提取金额——正式版
# from bs4 import BeautifulSoup
# def clear(x):
#     x = x.replace("人民币", "").replace(",", "").replace("￥", "").replace("4.报价", "").replace(" ", "").replace("总投资约","").replace("(","").replace(")","").replace("（","").replace("）","")
#     return x
#
# def delete(x):
#     for word in ["一、","二、", "三、", "四、", "五、", "六、", "七、", "八、", "九、", "十、", "1、", "2、", "3、", "4、", "5、", "6、", "7、", "8、", "9、", "10、"]:
#         if word in x:
#             x = x.replace(x[x.index(word):], "")
#         else:
#             x = x
#     return x
# %%
from bs4 import BeautifulSoup

def append_new(html,html_list,order,every,obj_list):
    if html_list.index(html) not in order and every != "":
        order.append(html_list.index(html))
        obj_list.append(every)
        print(every)
        print(html_list.index(html))
def han(x_new):
    if x_new=="":
     return "."
    m1 = []
    xlen = len(x_new)
    keywords2 = ["壹", "贰", "叁", "肆", "伍", "陆", "柒", "捌", "玖", "拾", "佰", "仟","万","圆","元","零"]
    for num in range(0,xlen-1):
        for symbol in keywords2 :
            if x_new[num]=="圆" and x_new[num+1]=="万" or  x_new[num]=="圆" and x_new[num+1]=="元":
               x_new[num + 1]=""
            else:
             if x_new[num]==symbol and x_new[num+1] in keywords2:
                m1.append(x_new[num])
            # else:
            #     m1.append(x_new[num+1])
            # break
    amount1 = ''.join(m1)
    amount1=amount1+"元"
    return amount1

def arabia(x_new):
    #读取进入的元素，对于没有单位的，连续三个数字以上才会识别数字；
    m2 = []
    if x_new=="":
     return "."
    #keywords1 = ["万", "1", "2", "3", "4", "5", "6", "7", "8", "9", "0", ".", "元"]
    keywords1= ["1", "2", "3", "4", "5", "6", "7", "8", "9", "0", ".", ","]
    if "元" in x_new and "%" in x_new:
          #index(symbol)
          if x_new.find("元")> x_new.find("%"):
              x_new = x_new[x_new.find("%")+1:]
          else:
              x_new = x_new[0:x_new.find("%")+1]
    else:
        x_new=x_new

    mark=len(x_new)-1   #抓到元后向前读
    if x_new[len(x_new)-1]=="元":
        for num in range(0, len(x_new) - 1):
            mark=mark-1
            if x_new[mark] in keywords1 and x_new[mark-1] not in keywords1:
               break
        x_new=x_new[mark:]

    if "万元" in x_new :  #and x_new[x_new.index("万元")-1] in keywords1
        for num in range(0, len(x_new) - 1):
          for symbol in keywords1:
            if x_new[num] == symbol :#and x_new[num + 1] in keywords1 and x_new[num + 2] in  keywords1
                m2.append(x_new[num])
            # if x_new[num] == symbol and x_new[num + 1] in keywords1 and x_new[num + 2] not in keywords1:
            #     if x_new[num - 1] in keywords1:
    else:
        for num in range(0,len(x_new) - 3):
            for symbol in keywords1:
                if x_new[num] == symbol and x_new[num + 1] in keywords1 and x_new[num + 2] in keywords1 and x_new[num + 3] in keywords1:
                    m2.append(x_new[num])
                if x_new[num] == symbol and x_new[num + 1] in keywords1 and x_new[num + 2] in keywords1 and x_new[num + 3] not in keywords1:
                    if x_new[num-1] in keywords1:
                     m2.append(x_new[num])
                     m2.append(x_new[num+1])
                     m2.append(x_new[num + 2])

    amount1 = ''.join(m2)

    if "万元" in x_new and "万元" not in amount1 :
         amount1 = amount1 + "万元"
         return amount1
    if "元" in x_new and "元" not in amount1 :
         amount1 = amount1 + "元"
         return amount1
    if "万" in x_new and "万" not in amount1 :
        amount1 = amount1 + "万"
        return amount1

    if amount1 == "":
        amount1 = "."
        return amount1
    return amount1

def arabia_percentile(x_new):
    if x_new=="":
     return "."
    m2 = []
    b=0
    xlen = len(x_new)
    # keywords1 = ["万", "1", "2", "3", "4", "5", "6", "7", "8", "9", "0", ".", "元"]
    keywords1 = ["%", "1", "2", "3", "4", "5", "6", "7", "8", "9", "0", "."]
    for num in range(0, xlen - 1):
        for symbol in keywords1:
            if x_new[num] == symbol and x_new[num + 1] in keywords1:
                m2.append(x_new[num])
                b+=1
    amount1 = ''.join(m2)
    if "%" in x_new:
     amount1 = amount1 + "%"
    if b == 0:
       amount1 = "."
    return amount1
    # id = 'WeWillbeTogetherForeverbe13093128'
    # pos = id.find('be')
    # count = 0  # 此处id.find返回的参数为字符在其出现的位置
    # while pos != -1:
    #     count = count + 1
    #     print(pos)
    #     print(id[pos + len("be"):pos + len("be") + 15])
    #     pos = (id.find('be', pos + 1))
    # # print('be共出现了{}次'.format(id.count('be')))
def exclude(x_new):
   if "%" in x_new:
       return arabia_percentile(x_new)

   if len(han(x_new)) >= 3:
       return han(x_new)
   else:
       if len(arabia(x_new)) >= 3 and len(arabia(x_new)) <= 20:
        return arabia(x_new)
       else:
           if len(arabia(x_new)) >=20:
               if "." in x_new:
                 return arabia(x_new[0:x_new.index(".")+4])   #保留"."后两位
               else:
                 return "."
           else:
                if len(arabia_percentile(x_new)) >=2:
                    return arabia_percentile(x_new)
                else:
                    return "."

def clear(x_new):
       # str.replace(u’\xa0’, u’ ‘)
       # str.replace(u’\u3000’, u’ ‘)
        x_new= x_new.replace("\u3000", "").replace("\xa0", "").replace("，", "").replace(",", "")
        return x_new

def amount_stop(x_new):
    global num
    keywords1 = ["%", "1", "2", "3", "4", "5", "6", "7", "8", "9", "0", ".", ",", "，"]
    xlen = len(x_new)
    for num in range(0, xlen - 5):
      if x_new[num] in keywords1 and x_new[num + 1] in keywords1 and x_new[num + 2] in keywords1 and x_new[num + 3] not in keywords1:
         break
    x_new1 = x_new[0:num + 5]
    x_new1 = x_new1+"it'shardtowritegoddamncode"
    return x_new1
def delete_wrong_num(x_new):
    keywords = ["2013","2012", "2011", "2010", "2009", "2008", "2007", "2006", "2005", "2004", "2003"]
    keywords2 = ["壹", "贰", "叁", "肆", "伍", "陆", "柒", "捌", "玖", "拾", "佰", "仟", "万", "零"]
    if x_new[0]=="0":
     for num in range (0,len(x_new)-2):
      if x_new[num]=="0" and x_new[num+1]!="0":
          x_new=x_new[num+1:]
          break
    if len(x_new)<=3:
        return x_new
    if x_new in keywords or x_new[0:len(x_new)-1] in keywords:
        return("")
    if x_new[1] in keywords2:
        return (x_new)
    if "." not in x_new:
     if  len(x_new)>8:
         x_new=x_new+"(大数待验)"
         return x_new
     else:
         return x_new
    else:
         x_new1=x_new[0:x_new.index(".")]
         if len(x_new1)>8:
            x_new = x_new + "(大数待验)"
            return x_new
         else:
             return x_new

amount_new = []
case_order_amount_new = []
amount_every_new = "."
number = ["1","2","3","4","5","6","7","8","9","0",",","，"]
m=0
amount_symbol = ["金额","￥","价","公司"]
keywords1 = ["万", "1", "2", "3", "4", "5", "6", "7", "8", "9", "0", ".", "元"]
keywords2 = ["壹", "贰", "叁", "肆", "伍", "陆", "柒", "捌", "玖", "拾", "佰", "仟", "万", "圆", "元","零"]
amount_symbol1 = ["壹","贰","叁","肆","伍","陆","柒","捌","玖","拾","佰","仟","万","1","2","3","4","5","6","7","8","9","0","."]
#第1包（也就是标包1 、第一标段、标段1 、A包、第一包等等）
for i in content_html[:crawl_number]:
    print("第"+str(m)+"html文件")
    m+=1
    i_txt = BeautifulSoup(i).get_text()
    i_txt.replace("单元", "").replace("竞价", "")
    if "第2包" in i_txt or "标包2" in i_txt or "第二标段" in i_txt or "标段2" in i_txt or "B包" in i_txt or "B类" in i_txt or "第2包" in i_txt or "第二包" in i_txt or "子包二" in i_txt:
        amount_every_new = "多标"
        append_new(i, content_html, case_order_amount_new, amount_every_new, amount_new)
    else:
        if "失败" in i_txt or "终止" in i_txt or "废标" in i_txt or "流标" in i_txt or "不足三家" in i_txt:
            amount_every_new = "废标"
            append_new(i, content_html, case_order_amount_new, amount_every_new, amount_new)
    if content_html.index(i) not in case_order_amount_new:
        for symbol in amount_symbol:
            flag = 0
            if symbol in i_txt:
                print("关键词是" + symbol)
                pos = i_txt.find(symbol)  ##循环定位关键字，找不到合适的接着找
                while pos != -1:
                    flag = 0
                    print("flag是" + str(flag))
                    if "元" in i_txt[pos + len(symbol):]:
                        # print(i_txt[pos + len(symbol):pos + len(symbol) + 15])
                        #amount_every_new = i_txt[i_txt.index("元") - 15:i_txt.index("元") + 1]
                        amount_every_new = i_txt[pos + len(symbol):pos + len(symbol) + i_txt[pos + len(symbol):].index("元") + len("元")]
                        amount_every_new = clear(amount_every_new)
                        amount_every_new = exclude(amount_every_new)
                        print("pos1是" + str(pos))
                        if len(amount_every_new) >= 4 or len(amount_every_new) >= 2 and "%" in amount_every_new:
                            flag = 1
                            print("一次就找到了理想金额,金额输出" + amount_every_new)
                            amount_every_new= delete_wrong_num(amount_every_new)
                            append_new(i, content_html, case_order_amount_new, amount_every_new, amount_new)
                            pos = (i_txt.find(symbol, pos + 1))
                            break

                        else:
                            amount_every_new = i_txt[pos + len(symbol):]
                            print("pos2是" + str(pos))
                            print("定位词‘元’不好用，变成关键句，是" + amount_every_new)
                            amount_every_new = clear(amount_every_new)
                            amount_every_new = amount_stop(amount_every_new)
                            amount_every_new = exclude(amount_every_new)
                            if len(amount_every_new) >= 4 or len(amount_every_new) >= 2 and "%" in amount_every_new:
                                print("两次就找到了理想金额,金额输出" + amount_every_new)
                                amount_every_new = delete_wrong_num(amount_every_new)
                                append_new(i, content_html, case_order_amount_new, amount_every_new, amount_new)
                                pos = (i_txt.find(symbol, pos + 1))
                                flag = 1
                                break
                            else:
                                # if len(amount_every_new) < 3 and amount_every_new[0] not in keywords1 or amount_every_new[0] not in keywords2 and amount_every_new[0]!="废" and amount_every_new[0]!="多":
                                #    print(pos)
                                #    pos = (i_txt.find(symbol, pos + 1))
                                #    print("没找到合适的金额，准备跳出2")
                                amount_every_new = i_txt[pos + len(symbol) - 20:pos + len(symbol)]
                                print("symbol反向输出" + symbol)
                                print("金额反向输出" + amount_every_new)
                                amount_every_new = clear(amount_every_new)
                                amount_every_new = exclude(amount_every_new)
                                if len(amount_every_new) >= 4 or len(amount_every_new) >= 2 and "%" in amount_every_new:
                                    print("三次才找到了理想金额,金额输出" + amount_every_new)
                                    amount_every_new = delete_wrong_num(amount_every_new)
                                    append_new(i, content_html, case_order_amount_new, amount_every_new, amount_new)
                                    pos = (i_txt.find(symbol, pos + 1))
                                    flag = 1
                                    break
                                else:
                                    break
                    else:
                        amount_every_new = i_txt[pos + len(symbol):]
                        print("pos2是" + str(pos))
                        print("定位词‘元’不好用，变成关键句，是" + amount_every_new)
                        amount_every_new = amount_stop(amount_every_new)
                        amount_every_new = clear(amount_every_new)
                        amount_every_new = exclude(amount_every_new)
                        if len(amount_every_new) >= 4 or len(amount_every_new) >= 2 and "%" in amount_every_new:
                            print("四次就找到了理想金额,金额输出" + amount_every_new)
                            amount_every_new = delete_wrong_num(amount_every_new)
                            append_new(i, content_html, case_order_amount_new, amount_every_new, amount_new)
                            pos = (i_txt.find(symbol, pos + 1))
                            flag = 1
                            break
                        else:
                            # if len(amount_every_new) < 3 and amount_every_new[0] not in keywords1 or amount_every_new[0] not in keywords2 and amount_every_new[0]!="废" and amount_every_new[0]!="多":
                            #    print(pos)
                            #    pos = (i_txt.find(symbol, pos + 1))
                            #    print("没找到合适的金额，准备跳出2")
                            amount_every_new = i_txt[pos + len(symbol) - 20:pos + len(symbol)]
                            print("symbol反向输出" + symbol)
                            print("金额反向输出" + amount_every_new)
                            amount_every_new = clear(amount_every_new)
                            amount_every_new = exclude(amount_every_new)

                            if len(amount_every_new) >= 4 or len(amount_every_new) >= 2 and "%" in amount_every_new:
                                print("找了五次才找到了理想金额,金额输出" + amount_every_new)
                                amount_every_new = delete_wrong_num(amount_every_new)
                                append_new(i, content_html, case_order_amount_new, amount_every_new, amount_new)

                                pos = (i_txt.find(symbol, pos + 1))
                                flag = 1
                                break
                            else:
                                amount_every_new = "."
                                amount_every_new = delete_wrong_num(amount_every_new)
                                append_new(i, content_html, case_order_amount_new, amount_every_new, amount_new)

                                pos = (i_txt.find(symbol, pos + 1))

                            break
                if flag == 1:
                    print("flag为1，金额是" + str(amount_every_new))
                    print("flag为1，我要跳走啦" + str(pos))
                    break
    if content_html.index(i) not in case_order_amount_new:
        amount_every_new = "."
        append_new(i, content_html, case_order_amount_new, amount_every_new, amount_new)
repair(case_order_amount_new,amount_new)




#%%
#供应商提取code
from bs4 import BeautifulSoup

def blank_space_stop(x):

    y=len(x)-1
    proxy_symbol = ["招标","投标", "代理", "工程管理", "招（投）标","工程咨询","工程造价","项目管理","工程项目咨询","工程造价咨询","投资咨询","招投标","工程监理","采购"]
    for num in range(0,len(x)-1):
        y-=1
        if '\u4e00' <= x[y] <= '\u9fff' and x[y-1]==" " or x[y-1]=="：" or x[y-1]==":" or  x[y-1]=="，" or  x[y-1]=="、" or  x[y-1]=="；":
        #if x[y]!= " "and x[y] != "：" and x[y - 1] == " " or x[y - 1] == "：":
             print(y)
             x=x[y:]
             for symbol in proxy_symbol:
                 if symbol in x:
                     print("代理公司")
                     x="."
                     return (x)
             else:
                 return (x)
        # if y==0:
        #     for symbol in proxy_symbol:
        #         if symbol in x:
        #             print("代理公司")
        #             x = "."
        #             return (x)
    else:
        print("公司前识别不到冒号或空格，代理企业去除,非代理企业输出原字符串")
        for symbol in proxy_symbol:
            if symbol in x:
                print("代理公司")
                x = "."
                return (x)
        else:
            return (x)


def append_new(html,html_list,order,every,obj_list):
    if html_list.index(html) not in order and every != "":
        order.append(html_list.index(html))
        obj_list.append(every)
        print(every)
        print(html_list.index(html))

def company(x):
#x="公司国赛u哦返回哦啊返回阿道夫书看了`   去安抚高压水更何况代理公司"
#x="公开招标方式进行采购。现就本次招标的结果公告如下：一、项目名称：柳州市本级土地整治规划编制项目二、项目编号：LZG12-289三、项目简要说明： 项号 采购内容 单 位 数 量 01 土地整治规划编制服务采购（负责规划的具体编制工作。按照《柳州市区土地整治规划编制工作方案》的要求，根据国家有关规划编制规程提供符合要求的规划成果资料。） 项 1 四、招标公告媒体及日期：2012年11月29日在中国政府采购网、广西财政网、柳州市政府采购网。五、评标信息：1、评标日期：2012年12月19日2、评标地点：柳州市河东路1号秀景园底层住宅区5-10号3、评标委员会成员：陈莉娴、彭建强、余卫平、莫红文、曾祥辉六、中标信息：中标供应商名称：广西壮族自治区国土资源规划院中标供应商地址：南宁市中新路2号中标金额：人民币伍拾玖万伍仟元整（￥595,000.00）七、联系事项：采购人：柳州市国土资源局地址：广西柳州市立新路49号联系人：曾主任    联系电话：0772-2813723采购代理机构：广西国泰招标咨询有限公司  地址：柳州市河东路1号秀景园底层住宅区5-10号联系人：陈 昊      联系电话：0772-3263818八、公告说明：1、投标人对中标公告有异议的，在中标公告发布之日起七个工作日内，以书面形式向采购人或采购代理机构提出质疑。       2、质疑人必须按《广西壮族自治区政府采购供应商质疑处理办法（桂财采〔2006〕11号文）的规定和招标文件“投标人须知“第一条第（九）款的要求来进行质疑，如不按政府采购相关规定质疑的，视为无效质疑，不予受理。广西国泰招标咨询有限公"
    pos = x.find("公司")  ##循环定位关键字，找不到合适的接着找
    while pos != -1:
         if "公司" in x and len(x[:pos + len("公司")]) >= 20:
            print("关键字是公司")
            seller_every = x[pos + len("公司") - 20:pos + len("公司")]
            seller_every = blank_space_stop(seller_every)
            if seller_every == ".":
                pos = (x.find("公司", pos + 1))
                print("pos1是" + str(pos))
            else:
                 if len(seller_every) >= 5:
                    print("找到了理想供应商,供应商输出" + seller_every)
                    return seller_every
                 else:
                    seller_every = "."
                    pos = (x.find("公司", pos + 1))
         else:
            pos = (x.find("公司", pos + 1))
    if pos == -1:
        print("没找到")
        seller_every = "."
        return seller_every




def academy(x):
 pos = x.find("院")
 while pos != -1:
      if "院" in x and len(x[:pos + len("院")]) >= 20:
        ##循环定位关键字，找不到合适的接着找
            print("关键字是院")
            seller_every = x[pos + len("院") - 20:pos + len("院")]
            seller_every = blank_space_stop(seller_every)
            if len(seller_every) >= 5:
                print("找到了理想供应商,供应商输出" + seller_every)
                pos = (x.find("院", pos + 1))
                return seller_every
            else:
                pos = (x.find("院", pos + 1))
      else:
       pos = (x.find("院", pos + 1))
 if pos == -1:
    x="."
    print("没找到")
    return x

def factory(x):
 pos = x.find("厂")
 while pos != -1:
      if "厂" in x and len(x[:pos + len("厂")]) >= 20:
        ##循环定位关键字，找不到合适的接着找
            print("关键字是厂")
            seller_every = x[pos + len("厂") - 20:pos + len("厂")]
            seller_every = blank_space_stop(seller_every)
            if len(seller_every) >= 5:
                print("找到了理想供应商,供应商输出" + seller_every)
                pos = (x.find("厂", pos + 1))
                return seller_every
            else:
                pos = (x.find("厂", pos + 1))
      else:
       pos = (x.find("厂", pos + 1))
 if pos == -1:
    x="."
    print("没找到")
    return x

def market(x):
     pos = x.find("商场")
     while pos != -1:
         if "商场" in x and len(x[:pos + len("商场")]) >= 20:
             ##循环定位关键字，找不到合适的接着找
             print("关键字是商场")
             seller_every = x[pos + len("商场") - 20:pos + len("商场")]
             seller_every = blank_space_stop(seller_every)
             if len(seller_every) >= 5:
                 print("找到了理想供应商,供应商输出" + seller_every)
                 pos = (x.find("商场", pos + 1))
                 return seller_every
             else:
                 pos = (x.find("商场", pos + 1))
         else:
             pos = (x.find("商场", pos + 1))
     if pos == -1:
         x = "."
         print("没找到")
         return x

def  eliminate(x):
    x=x.replace("1", "").replace("2", "").replace("3", "").replace("4", "").replace("5", "").replace("6", "").replace("7", "").replace("8", "").\
        replace("9", "").replace("关于", "").replace("评标委员会", "").replace("推荐", "").replace("px", "").replace("}", "").replace(" ", "").replace("}", "").\
        replace("拟中标单位", "").replace("成交候选人", "").replace("拟成交", "").replace("评标委员会", "").replace("本项目", "").replace(";", "").replace("{", "").replace("中标公告", "").replace("标公告", ""). \
        replace("关于", "").replace("中标", "").replace("共有", "").replace("一包", "").replace("A01", "").replace("保险公司","").replace("下列", "").replace("标的", "").replace("中标", "")
    return x
    #     replace("A", "")\
    # .replace("B", "").replace("C", "").replace("D", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "") \
    #     .replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "") \
    #     .replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "") \
    #     .replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "").replace("}", "")\





seller = []
case_order_seller = []
seller_every = ""
proxy_symbol=["招标","代理","工程管理","招（投）标"]
#seller_symbol=["厂","研究院","大学","公司"]
m=0
for i in content_html[:crawl_number]:
    print("第" + str(m) + "html文件")
    m += 1
    i_txt = BeautifulSoup(i).get_text()
    i_txt = i_txt.replace("一家公司", "").replace("原厂", "").replace("生产厂", "").replace("我公司", "").replace("一家公司", "")
    if "第2包" in i_txt or "标包2" in i_txt or "第二标段" in i_txt or "标段2" in i_txt or "B包" in i_txt\
            or "B类" in i_txt or "第2包" in i_txt or "第二包" in i_txt or "子包二" in i_txt or "包件二" in i_txt\
            or "品目2" in i_txt:
        seller_every = "多标"
        append_new(i, content_html, case_order_seller, seller_every, seller)
    else:
        if "失败" in i_txt or "终止" in i_txt or "废标" in i_txt or "流标" in i_txt or "不足三家" in i_txt:
            seller_every = "废标"
            append_new(i, content_html, case_order_seller, seller_every, seller)
    if content_html.index(i) not in case_order_seller:
        if  len(company(i_txt))>=5:
            seller_every=company(i_txt)
            seller_every=eliminate(seller_every)
            append_new(i, content_html, case_order_seller, seller_every, seller)
            continue
        else:
         if  len(factory(i_txt))>=5:
             seller_every = factory(i_txt)
             seller_every = eliminate(seller_every)
             append_new(i, content_html, case_order_seller, seller_every, seller)
             continue
         else:
             if len(academy(i_txt))>=5:
                 seller_every = academy(i_txt)
                 seller_every = eliminate(seller_every)
                 append_new(i, content_html, case_order_seller,seller_every, seller)
                 continue
             else:
                 if len(market(i_txt)) >= 5:
                     seller_every = academy(i_txt)
                     seller_every = eliminate(seller_every)
                     append_new(i, content_html, case_order_seller, seller_every, seller)
                     continue
    if content_html.index(i) not in case_order_seller:
        seller_every = "."
        append_new(i, content_html, case_order_seller, seller_every, seller)
repair(case_order_seller, seller)

#%%
#采购人提取code
# -*- coding:utf-8 -*-
from bs4 import BeautifulSoup
def fetch(x):
    symbol=["局",]

def clear(x):
    x = x.replace("（以下简称“采购人”）", "").replace(" ", "").replace("采购人", "").replace("称：", "").replace("采购单位", "").replace(
        "()", "").replace("的", "").replace("二、", "").replace("财政部唯一", "").replace("\n", "")
    return x

def delete(x):
    for word in ["联系", "联 系", "联  系", "地址", "联系电话", "联系方式", "招标", "集采", "开标", "址：", "采购", "代理", "业主", "人：", "(", ")",
                 "项目", "委托", "定点", "公开", "公务", "一标段", "工程", "投标", "电话","集中",
                 "（", "）", "二.", "三.", "四.", "五.", "六.", "七.", "八.", "九.", "十.",
                 "二、", "三、", "四、", "五、", "六、", "七、", "八、", "九、", "十、",
                 "1.", "2.", "3.", "4.", "5.", "6.", "7.", "8.", "9.", "10."
        , "1、", "2、", "3、", "4、", "5、", "6、", "7、", "8、", "9、", "10、"]:
        if word in x:
            x = x.replace(x[x.index(word):], "")
        else:
            x = x
    return x

case_order_buyer = []
buyer = []
case_order = []
buyer_every = ""

for i in content_html[:crawl_number]:
    i_txt = BeautifulSoup(i).get_text()
    # 循环定位
    position = {"采购人": "行政区域", "采购单位": "行政区域", "公司受": "的委托", "公司受": "委托", "受": "的委托", "受": "委托", "(重发）": "的采购结果公告",
                "中央预算内投资": "续建"}
    symbols = ["招标人：", "招标人:", "招 标 人：", "招标人名称：", "招标人名称:", "采购人名称：", "采购人名称:", "采购人：",
               "采购人:", "采  购   人：", "采 购 人：", "采 购 人:", "采购单位名称：", "业主单位名称：", "业主单位名称:",
               "招 标 人:", "采购单位：", "采购单位:", "项目单位：", "项目单位:", "建设单位：", "采购人单位名称：", "采购人单位名称:", "采购机关：", "采购机关:",
               "采购人名称", "招标人名称", "采购单位名称", "业主单位名称", "项目单位名称：", "建设单位名称：", "采购机关名称：", "项目单位名称:", "建设单位名称:", "采购机关名称:",
               "供货。", "完毕。", "采 购 人", "采购机构：", "采购机构:"]
    for former, latter in position.items():
        if former in i and latter in i[i.index(former):]:
            buyer_every = ""
            pos_1 = i.index(former)
            pos_2 = i[pos_1:-1].index(latter) + pos_1
            if i[pos_1 + len(former):pos_2] in ["招标人", "采购人", "业主单位", "采购机关", "采购单位", "项目单位", "建设单位", "招标人的", "采购人的",
                                                "业主单位的", "采购机关的", "采购单位的", "项目单位的", "建设单位的"]:
                pass
            else:
                buyer_every = clear(delete(i[pos_1 + len(former):pos_2]))
                if content_html.index(i) not in case_order and buyer_every != "":
                    case_order.append(content_html.index(i))
                    append_new(i, content_html, case_order_buyer, buyer_every, buyer)
                else:
                    break
                print(buyer_every)
                print(content_html.index(i))
                break
        elif "中央国家机关" in i:
            buyer_every = "."
            if content_html.index(i) not in case_order and buyer_every != "":
                case_order.append(content_html.index(i))
                append_new(i, content_html, case_order_buyer, buyer_every, buyer)
            else:
                break
            print(buyer_every)
            print(content_html.index(i))
            break
        elif symbols[0] in i or symbols[1] in i or symbols[2] in i or symbols[3] in i or symbols[4] in i or symbols[
            5] in i or symbols[6] in i or symbols[7] in i or symbols[8] in i or symbols[9] in i or symbols[10] in i or \
                symbols[11] in i or symbols[12] in i or symbols[13] in i or symbols[14] in i or symbols[15] in i or \
                symbols[16] in i or symbols[17] in i or symbols[18] in i or symbols[19] in i or symbols[20] in i or \
                symbols[21] in i or symbols[22] in i or symbols[23] in i or symbols[24] in i or symbols[25] in i or \
                symbols[26] in i or symbols[27] in i or symbols[28] in i or symbols[29] in i or symbols[30] in i or \
                symbols[31] in i or symbols[32] in i or symbols[33] in i or symbols[34] in i or symbols[35] in i or \
                symbols[36] in i or symbols[37] in i or symbols[38] in i or symbols[39] in i:
            for symbol in symbols:
                if symbol in i:
                    buyer_every = ""
                    pos_1 = i.index(symbol)
                    buyer_every = clear(delete(i[pos_1 + len(symbol):pos_1 + len(symbol) + 30]))
                    if content_html.index(i) not in case_order and buyer_every != "":
                        case_order.append(content_html.index(i))
                        append_new(i, content_html, case_order_buyer, buyer_every, buyer)
                    else:
                        break
                    print(buyer_every)
                    print(content_html.index(i))
                    break
        else:
            pass
    if buyer_every==".":

        if content_html.index(i) not in case_order_buyer and buyer_every != "":
            case_order.append(content_html.index(i))
            append_new(i, content_html, case_order_buyer, buyer_every, buyer)
            print(buyer_every)
            print(content_html.index(i))

    if "失败" in i or "终止" in i or "废标" in i or "流标" in i or "不足三家" in i:
        buyer_every = "."
        if content_html.index(i) not in case_order_buyer and buyer_every != "":
            case_order.append(content_html.index(i))
            append_new(i, content_html, case_order_buyer, buyer_every, buyer)
            print(buyer_every)
            print(content_html.index(i))
        else:
            pass
    else:
        pass
# if content_html.index(i) not in case_order_seller:
#     seller_every = "."
#     append_new(i, content_html, case_order_buyer, buyer_every, buyer)
repair(case_order_buyer, buyer)
#%%
#提取竞争对手
def clear(x):
    for ch in x:
        if ch < '\u4e00'  or ch > '\u9fa5':
            x = x.replace(ch, "")
    x=x.replace("发布人","").replace("宋体","").replace("成交供应商","").replace("中标供应商","").replace("代理机构","").replace("名称","").replace("单位","")
    return x

rival = []
case_order_rival = []
rival_every = ""

for i in content_html[:crawl_number]:
    rival_every = ""
    if "公司" in i:
        text = i
        for number in range(i.count("公司")):
            if "公司" in text:
                pos_end = text.rindex("公司")
                for c in text[pos_end-20:pos_end]:
                    if c < '\u4e00'  or c > '\u9fa5':
                        pos_start = text[:pos_end].rindex(c)
                        if rival_every == "" and "投标" not in text[pos_start + len(c):pos_end + 2]and "招标" not in text[pos_start + len(c):pos_end + 2] and "代理" not in text[pos_start + len(c):pos_end + 2]\
                                    and "发布人" not in text[pos_start + len(c):pos_end + 2] and "宋体" not in text[pos_start + len(c):pos_end + 2]\
                                    and "成交" not in text[pos_start + len(c):pos_end + 2] and "中标" not in text[pos_start + len(c):pos_end + 2]\
                                    and "名称" not in text[pos_start + len(c):pos_end + 2] and "单位" not in text[pos_start + len(c):pos_end + 2]:
                            rival_every = clear(text[pos_start+len(c):pos_end+2])
                        else:
                            if clear(text[pos_start + len(c):pos_end + 2]) not in rival_every and "投标" not in text[pos_start + len(c):pos_end + 2]and "招标" not in text[pos_start + len(c):pos_end + 2] and "代理" not in text[pos_start + len(c):pos_end + 2]\
                                    and "发布人" not in text[pos_start + len(c):pos_end + 2] and "宋体" not in text[pos_start + len(c):pos_end + 2]\
                                    and "成交" not in text[pos_start + len(c):pos_end + 2] and "中标" not in text[pos_start + len(c):pos_end + 2]\
                                    and "名称" not in text[pos_start + len(c):pos_end + 2] and "单位" not in text[pos_start + len(c):pos_end + 2]:
                                rival_every = rival_every + ";" + clear(text[pos_start + len(c):pos_end + 2])
                        break
            text = text[:pos_start]
        if content_html.index(i) not in case_order_rival:
            if rival_every == "":
                rival_every = "."
            case_order_rival.append(content_html.index(i))
            rival.append(rival_every)
            print(rival_every)
            print(content_html.index(i))
    else:
        if content_html.index(i) not in case_order_rival:
            rival_every = "."
            case_order_rival.append(content_html.index(i))
            rival.append(rival_every)
            print(rival_every)
            print(content_html.index(i))

print(len(rival))





# %%
#合并并输出csv文件
import pandas as pd
print(len(title))
#print(len(date_deal))


pathout = 'C:\\Users\\mycro.SHEERAN_CORE\\desktop\\GP_NEW\\'
# raw_2015 = pd.DataFrame({"公告名称": title, "采购结果": result, "采购方式": method, "公告日期": date_deal,"开标日期":date_pro,
#                          # "采购人地址":address_buyer,
#                          "采购人":buyer,"供应商":seller,"中标金额":amount,"竞争对手":rival})
file = pd.DataFrame({"公告名称": title,"成交状态":result,"成交公示日期":date_public,"订单发布日期":date_deal,"采购方式":method,"金额":amount_new,"供应商":seller,"采购人":buyer,"竞争对手":rival})
file.to_csv(pathout+"testwhole.csv",encoding='utf_8_sig')
