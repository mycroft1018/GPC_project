# GPC_project
# the use for this module is to extract the amount data from China government procurement contracts (before 2012)
# this text provide a basic function of GPC-database-extraction code, i wrote this code mostly by myself
# beacuse my major is econ and my code skills is rather lame, this code is easy and simple, the extraction result is also rather mundane
# due to the need for confidentiality, i won't provide the whole part of GPC_code or the py file

# %%
#amount extraction

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


