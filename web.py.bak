from difflib import get_close_matches
from flask import Flask, render_template, request, Response
import json
import os
import random
from datetime import datetime


app = Flask(__name__)

filename = os.path.join(app.static_folder, 'data.json')
with open(filename) as data_file:
    data = json.load(data_file)

search_history = []
rever_norepeat_history = []

@app.route("/")
def homepage():
    random_word = random.sample(list(data.keys()),3)        #辞書リストから任意の３つの単語をランダムで抽出する
    statement = "Daily Words: 下記の単語、知っていますか？"
    print(random_word)
    return render_template("index.html", random_words = random_word, statement = statement)

@app.route("/words", methods = ["POST", "GET"])
def search():
    if request.method == "GET":
        word = request.args.get("search_word")
        result = search_word_function(word)
        print(result)
        
        if isinstance(result,str):
            return render_template("index.html", notfound_statement = result)
        else:
            if len(result) == 3:
                return render_template("index.html", notfound_statement = result[1], alternative_list = result[2])
            else:
                global search_history 
                search_history.append([result[0],result[1]])
                return render_template("index.html", result_word = result[0],search_result = result[1])
    
@app.route("/history/")
def history():
    print("history功能被使用")
    norepeat_history = removerepe()
    return render_template("history.html", word_history = norepeat_history)

@app.route("/delete_history/", methods = ["POST"])
def delete_history():
    print("delete_history功能被使用")
    global search_history 
    global rever_norepeat_history
    search_history = []
    rever_norepeat_history = []
    print("履历全部清除完毕！！")
    print("search_history是：")
    print(search_history)
    print("rever_norepeat_history是：")
    print(rever_norepeat_history)
    return render_template("history.html")

@app.route("/history/download_excel/", methods = ["GET"])
def download_excel():
     today_date = getToday()
     words_csv = "Index,\tWord,\tDescription" + "\r\n"
     for index, value in enumerate(rever_norepeat_history):
        words_line = str(index+1) + ',\t' + value[0] + ',\t' + value[1][0] + '\r\n'
        words_csv += words_line
     return Response(words_csv,mimetype="text/csv",headers={"Content-disposition":"attachment; filename=MyWords("+ today_date  +").csv"})


@app.route("/history/download_txt/", methods = ["GET"])
def download_txt():
    global rever_norepeat_history
    today_date = getToday()
    words_txt = ""
    for index, value in enumerate(rever_norepeat_history):
        words_line = str(index+1) + '.' + value[0] + ': ' + value[1][0] + '\n'
        words_txt += words_line
    return Response(words_txt,mimetype="text/txt",headers={"Content-disposition":"attachment; filename=MyWords("+ today_date  +").txt"})
        


def search_word_function(word):
    print("search_word_function功能被使用")
    key_list = list(data.keys())
    value_list = list(data.values())

    if word in data.keys():
        position = value_list.index(data[word])
        return [key_list[position],data[word]]
    elif word.lower() in data.keys():
        position = value_list.index(data[word.lower()])
        return [key_list[position], data[word.lower()]]
    elif word.title() in data.keys():
        position = value_list.index(data[word.title()])
        return [key_list[position], data[word.title()]]
    elif word.upper() in data.keys():
        position = value_list.index(data[word.upper()])
        return [key_list[position], data[word.upper()]]
    elif len(get_close_matches(word,data.keys()))>0:
        statement = "「%s」が見つかりませんでした。もしかして:"%word
        return [word, statement, get_close_matches(word, data.keys())[:3]]
    else:
        return "すみません。「%s」が見つかりませんでした。入力した単語をもう一度ご確認ください。"%word

def removerepe():
    print("removerepe功能被使用")
    global search_history
    global rever_norepeat_history
    list = []
    for item in search_history:
        if item not in list:
            list.append(item)
    rever_norepeat_history = list[::-1]     #検索順で一番最近検索したものを上位順にするので、listを逆順にした
    print("removerepe功能返回的rever_norepeat_history是：")
    print(rever_norepeat_history)
    return rever_norepeat_history

def getToday():
    now = datetime.now()
    today = now.strftime("%Y-%m-%d")
    return today

if __name__ == "__main__":
    app.run(debug=True)