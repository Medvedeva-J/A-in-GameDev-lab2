# DA-in-GameDev-lab2
# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
СБОР, ОБРАБОТКА И ВИЗУАЛИЗАЦИЯ ТЕСТОВОГО НАБОРА ДАННЫХ.

Отчет по лабораторной работе #2 выполнил(а):
- Медведева Юлия Олеговна
- РИ-210940
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
Познакомиться с программными средствами для организции передачи данных между инструментами google, Python и Unity

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity. 
#### 1) В облачном сервисе google console подключить API для работы с google sheets и google drive.
- В облачном сервисе google console создала новый проект.
![image](https://user-images.githubusercontent.com/62373163/195047348-fddc8f96-3fb1-47e6-a336-1f008fed2e58.png)
![image](https://user-images.githubusercontent.com/62373163/195047824-ce128eea-49a2-449d-873c-8d963af517df.png)

- Подключила API для работы с google sheets и google drive.
![image](https://user-images.githubusercontent.com/62373163/195048600-5a6f8952-2a77-483f-92ff-958d19e5de5b.png)

- Создала сервисный аккаунт и ключ.
![image](https://user-images.githubusercontent.com/62373163/195049116-3e0ae877-ffb7-495a-886d-6a4c5a248259.png)

- Создала документ в google таблицах и предоставила доступ сервисному аккаунту.
![image](https://user-images.githubusercontent.com/62373163/195049552-02271e51-0bf0-47aa-9ed6-8af17e43d2b6.png)

#### 2) Реализовать запись данных из скрипта на python в google-таблицу. Данные описывают изменение темпа инфляции на протяжении 11 отсчётных периодов, с учётом стоимости игрового объекта в каждый период.
- Запустила код - полученные данные появились в таблице
![image](https://user-images.githubusercontent.com/62373163/195050074-a4e42bec-868f-4328-8fa3-e5d6f80f4d9a.png)
![image](https://user-images.githubusercontent.com/62373163/195050149-8ea98940-0314-4eee-ade9-b8fd17f5df3d.png)

#### 3) Создать новый проект на Unity, который будет получать данные из google-таблицы, в которую были записаны данные в предыдущем пункте.
- Создала новый проект и импортировала пакеты из материалов к лабораторной работе.
![image](https://user-images.githubusercontent.com/62373163/195052031-24a82cdc-a981-4cbc-8680-58a1ccaf34bd.png)

#### 4) Написать функционал на Unity, в котором будет воспризводиться аудио-файл в зависимости от значения данных из таблицы.
- Прописала в скрипте код из методических материалов к ЛР.
```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1KFC3nba9FJfv6Vq3GHt2nLatDDzqGkECBC2vi36s-_M/values/Лист1?key=AIzaSyBUMKnWhpCaNxjFiY8T3mJ2_3Ds5lJmEbE");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}
```

- Добавила аудио из импортированных файлов к скрипту
![image](https://user-images.githubusercontent.com/62373163/195052463-64b2299a-e1ba-437e-92bb-d7a66583abfd.png)

- Вывод программы:
![image](https://user-images.githubusercontent.com/62373163/195052825-8bac01df-7948-45dd-933f-439510034d00.png)
С каждой строкой вывода воспроизводится звук, соответствующий значению строки гугл-таблицы.


## Задание 2
### Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1. 
- Поправила код из ЛР-1

```py
import gspread
import numpy as np
import matplotlib.pyplot as plt

x = [3, 21, 22, 34, 54, 34, 55, 67, 89, 99]
x = np.array(x)
y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
y = np.array(y)

plt.scatter(x, y)


def model(a, b, x):
    return a * x + b


def loss_function(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    return (0.5 / num) * (np.square(prediction - y)).sum()


def optimize(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    da = (1.0 / num) * ((prediction - y) * x).sum()
    db = (1.0 / num) * ((prediction - y).sum())
    a = a - Lr * da
    b = b - Lr * db
    return a, b


def iterate(a, b, x, y, times):
    for i in range(times):
        a, b = optimize(a, b, x, y)
    return a, b

a = np.random.rand(1)
b = np.random.rand(1)
Lr = 0.000001

gc = gspread.service_account(filename='unitydatascience-365118-7456c414ecb4.json')
sh = gc.open("UnitySheets")
price = np.random.randint(2000, 10000, 11)
mon = list(range(1,11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        a, b = iterate(a, b, x, y, 100)
        prediction = model(a, b, x)
        loss = loss_function(a, b, x, y)
        loss = str(loss).replace('.', ',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(price[i-1]))
        sh.sheet1.update(('C' + str(i)), str(loss))
        print(loss)
```

- Добавила ссылку-ключ на новый проект
![image](https://user-images.githubusercontent.com/62373163/195056701-5e46a0b3-2953-477b-a150-072ddd6df7a4.png)

- Запустила код, чтобы записать в таблицу новые данные.
![image](https://user-images.githubusercontent.com/62373163/195056881-b85ece75-0dbd-415f-8458-9ba2933739fb.png)
![image](https://user-images.githubusercontent.com/62373163/195056963-5f850b0e-3974-418d-8b04-122da6fd1855.png)

## Задание 3
### Самостоятельно разработать сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2.
- Изменила пограничные значения, при которых воспроизводятся файлы.
![image](https://user-images.githubusercontent.com/62373163/195057762-9e7db9f6-f8d2-4d7b-9ac0-d6f48d3e4be3.png)

- Вывод в Unity
![image](https://user-images.githubusercontent.com/62373163/195059678-cd80b9ba-b577-46a4-bac8-80ca223a3b41.png)
Так же воспроизводится соответствующая аудиодорожка в зависимости от значения в таблице.


## Выводы
Я узнала о том, как обеспечивать совместную работу Unity, Python, Google Sheets; узнала о том, как работать с проектами в облачном сервисе google console; узнала об основах работы с API.
| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
