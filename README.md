# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил:
- Кармацкий Савелий Андреевич
- РИ210942
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Познакомиться с программными средствами для организции передачи данных между инструментами Google, Python и Unity

## Задание 1 Реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity.
Ход работы:
- Создать ключ в Google Сloud:
![Image alt](https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab2/main/Content/API%20Key.png)

- Подсоединение к таблицам:
```py
import gspread
import numpy as np
gs = gspread.service_account(filename='unitydatascience-364307-56bcea742b1.json')
sh = gc.open('UnitySheets')
price = np.random.randint(2000, 10000, 11)
mon = list(range(1, 11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        tempInf = ((price[i-1] - price[i - 2])/price[i-2]) * 100
        tempInf = str(tempInf)
        tempInf = tempInf.replace('.',',)
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(price[i-1]))
        sh.sheet1.update(('C' + str(i)), str(tempInf))
        print(tempInf)
```
Вывод: 

![Image alt](https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab2/main/Content/Vivod.png)

- Подключение таблицы к Unity:
```cs
void Update()
{
    if(dataSet["Mon_" + i.ToString()] <= 10 & starusStart == false & i != dataSet.Count)
    {
        StartCoroutine(PlaySelectAudioGood());
        Debug.Log(dataSet["Mon_" + i.ToString()]);
    }
    if(dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & starusStart == false & i != dataSet.Count)
    {
        StartCoroutine(PlaySelectAudioNormal());
        Debug.Log(dataSet["Mon_" + i.ToString()]);
    }
    if(dataSet["Mon_" + i.ToString()] >= 10 & starusStart == false & i != dataSet.Count)
    {
        StartCoroutine(PlaySelectAudioBad());
        Debug.Log(dataSet["Mon_" + i.ToString()]);
    }
}
IEnumerator GoogleSheets()
{
    UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadheets/1_I7x6IKv5cC7dommC_Hou-bKlmvt8k_8ZynVumB-1V0");
    yield return curentResp.SendWebRequest();
    string rawResp = curentResp.downloadHandler.text;
    var rawJson = JSON.Parse(rawResp);
    foreach(var itemRawJson in rawJson["values"])
    {
        var parseJson = JSON.Parse(itemRawJson.ToString());
        var selectRow = parseJson[0].AsStringlist;
        dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]);
    }
}
IEnumerator PlaySelectAudioGood()
{
    starusStart = true;
    selectAudio = GetComponent<AudioSource>();
    selectAudio.clip = goodSpeak;
    selectAudio.Play();
    yield return new WaitForSeconds(3);
    starusStart = false;
    i++;
}
IEnumerator PlaySelectAudioNormal()
{
    starusStart = true;
    selectAudio = GetComponent<AudioSource>();
    selectAudio.clip = normalSpeak;
    selectAudio.Play();
}
```

## Задание 2 Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1
Ход работы:
- Создание loss в цикле, после вывод в таблицу:
```py
def optimize(a,b,x,y):
    num = len(x)
    prediction = model(a,b,x)
    da = (1.0/num) * ((prediction -y)*x).sum()
    db = (1.0/num) * ((prediction -y).sum())
    a = a - Lr*da
    b = b - Lr*db
    return a, b

def iterate(a,b,x,y,times):
    for i in range(times):
        a,b = optimizel(a,b,x,y)
    return a,b

a = np.random.rand(1)
b = np.random.rand(1)
Lr = 0.000001

gc = gspread.service_account(filename='unitydatasciense-364307-S6bcea74a2b1.json')
sh = gc.open('UnitySheets')
price = np.random.randint(2000, 10000, 11)
mon = list(range(1, 11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        a,b = iterate(a,b,x,y,100)
        prediction=model(a,b,x)
        loss = loss_function(a, b, x, y)
        tempInf = loss
        tempInf = str(tempInf)
        tempInf = tempInf.replace('.',',')
        sh.sheet1.update(("A’ + str(i)), str(i))
        sh.sheeti.update(("B’ + str(i)), str(tempInf))

        print(tempInf)
```
Вывод в таблицу:

![Image Alt](https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab2/main/Content/Vivod2.png)

## Задание 3 Самостоятельно разработать сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2

Ход работы:
- Сначала надо изменить диапазон данных в коде, чтобы они смогли подойти под диапазон данных loss, после надо изменить количество колонок

Изменение диапозона и количества колонок:
```cs
void Update()
{
    if(dataSet["Mon_" + i.ToString()] <= 195 & starusStart == false & i != dataSet.Count) // Изменение диапозона
    {
        StartCoroutine(PlaySelectAudioGood());
        Debug.Log(dataSet["Mon_" + i.ToString()]);
    }
    if(dataSet["Mon_" + i.ToString()] > 195 & dataSet["Mon_" + i.ToString()] < 500 & starusStart == false & i != dataSet.Count) // Изменение диапозона
    {
        StartCoroutine(PlaySelectAudioNormal());
        Debug.Log(dataSet["Mon_" + i.ToString()]);
    }
    if(dataSet["Mon_" + i.ToString()] >= 500 & starusStart == false & i != dataSet.Count) // Изменение диапозона
    {
        StartCoroutine(PlaySelectAudioBad());
        Debug.Log(dataSet["Mon_" + i.ToString()]);
    }
}
IEnumerator GoogleSheets()
{
    UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadheets/1_I7x6IKv5cC7dommC_Hou-bKlmvt8k_8ZynVumB-1V0");
    yield return curentResp.SendWebRequest();
    string rawResp = curentResp.downloadHandler.text;
    var rawJson = JSON.Parse(rawResp);
    foreach(var itemRawJson in rawJson["values"])
    {
        var parseJson = JSON.Parse(itemRawJson.ToString());
        var selectRow = parseJson[0].AsStringlist;
        dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[1]); // Изменение кол-ва колонок
    }
}
```
Вывод в консоль: 

![Image alt] (https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab2/main/Content/Vivod3.png)

## Выводы
В ходе данной лабораторной работы я выяснил как можно подключить Google-таблицу к коду, как записывать в нее значения. Также что эту таблицу можно связать с Unity.

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

