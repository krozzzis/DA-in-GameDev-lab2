# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил(а):
- Шумов Никита Артёмович
- РИ232702  
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
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы

Научиться передавать в Unity данные из Google Sheets с помощью Python.

## Задание 1

Выберите одну из игровых переменных в игре СПАСТИ РТФ: Выживание (HP, SP, игровая валюта, здоровье и т.д.), опишите её роль в игре, условия изменения / появления и диапазон допустимых значений. Постройте схему экономической модели в игре и укажите место выбранного ресурса в ней.

### Описание переменной "Очки здоровья (HP)"

Роль в игре: Очки здоровья (HP) являются основной переменной, отражающей физическое состояние персонажа в игре. Потеря HP указывает на ущерб персонажу от враждебных факторов, таких как атаки врагов. 
Восстановление HP, наоборот, сигнализирует о лечении персонажа.

Условия изменения/появления:  
- HP уменьшаются при атаке врагов, попадании в ловушки, воздействии негативных эффектов окружающей среды.
- HP восстанавливаются при использовании аптечек, принятии пищи, отдыхе, или других действиях, восстанавливающих здоровье.

Диапазон допустимых значений:
- Обычно HP имеют диапазон от 0 (персонаж погиб) до максимально возможного значения, которое может варьироваться в зависимости от уровня персонажа или улучшений (например, 30 HP для базового уровня).
- Максимум может быть увеличен за счёт улучшений или экипировки.

## Задание 2

С помощью скрипта на языке Python заполните google-таблицу данными, описывающими выбранную игровую переменную в игре “СПАСТИ РТФ:Выживание”. Средствами google-sheets визуализируйте данные в google-таблице (постройте график / диаграмму и пр.) для наглядного представления выбранной игровой величины. Опишите характер изменения этой величины, опишите недостатки в реализации этой величины (например, в игре может произойти условие наступления эксплойта) и предложите до 3-х вариантов модификации условий работы с переменной, чтобы сделать игровой опыт лучше.

### Ход работы:

В Jupyter Notebook был создан новый `.ipynb` файл с следующим кодом

```python
import gspread
import numpy as np

gc = gspread.service_account(filename="unitydatascience-440608-bd401cd4f4af.json")
sh = gc.open("GameLab")

# Параметры симуляции
max_time = 17  # Время симуляции в секундах
initial_health = 30  # Начальное здоровье
damage = 3  # Урон
time_step_range = (2, 10)  # Диапазон времени между ударами
hit_count = 6

time = 0
health = initial_health
data = []
data.append([time, health])  # Начальное состояние
hits = set([np.random.randint(*time_step_range) for _ in range(hit_count)])

while time < max_time:
    # Генерация случайного шага времени в диапазоне от 2 до 10 секунд
    time += 1
    if time > max_time:
        break  # Прекращаем, если время превысило лимит

    # Уменьшаем здоровье и добавляем запись
    if time in hits:
        health = max(health - damage, 0)
    data.append([time, health])

for (i, row) in enumerate(data):
    print(i+1, row)
    sh.sheet1.update(('A' + str(i+1)), str(row[0]))
    sh.sheet1.update(('B' + str(i+1)), str(row[1]))
```
Он симулирует поведение очков здоровья. В случайные моменты времени игроку наносятся удары, которые отнимают его HP.

### Анализ изменения HP и предложения по улучшению

Характер изменения: HP уменьшаются в случае повреждений и восстанавливаются при лечении. Обычный график HP может быть ступенчатым, показывая спад при атаках и повышение при восстановлении.

Недостатки:
- Если восстановление HP слишком легкое, игроки могут быстро восстанавливать здоровье и не ощущать риск.
-  Если HP падают слишком быстро, это может вызвать чрезмерное напряжение или усложнение.
- Возможен эксплойт, при котором игроки злоупотребляют лечением, особенно если предметы восстанавливают полное здоровье.

Варианты модификаций:
- Ограничить использование лечебных предметов (например, временные ограничения или установление "усталости" после каждого использования).
- Динамически изменять максимальный HP в зависимости от уровня здоровья персонажа или среды, чтобы игрокам приходилось более стратегически подходить к использованию HP.
- Ввести постепенное восстановление вместо мгновенного лечения: например, при использовании аптечки HP восстанавливаются не сразу, а в течение некоторого времени, чтобы повысить реалистичность и сложность игры.

## Задание 3

Настройте на сцене Unity воспроизведение звуковых файлов, описывающих динамику изменения выбранной переменной. Например, если выбрано здоровье главного персонажа вы можете выводить сообщения, связанные с его состоянием.

Ход работы:

Создан скрипт `NewMonoBehaviourScript.cs`
```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewMonoBehaviourScript : MonoBehaviour
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
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 20 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 20 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("###");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[1]));
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
Он добавлен на пустой объект в сцене.

## Выводы

...

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
