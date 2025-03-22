# in -GameDev- 3
# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил(а):
- Тараснко Алексей Романович
- РИ-230913

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | ? |
| Задание 2 | * | ? |
| Задание 3 | * | ? |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)


## Цель работы
Разработать оптимальный баланс изменения сложности для десяти уровней игры Dragon Picker.

## Задание 1. Начало работы с прототипом
### Ознакомьтесь с презентацией третьей лекции, оцените структуры игры Dragon Picker. Скачайте и ознакомьтесь с прототипом игры Dragon Picker на движке Unity. Запустите игровую сцену, проанализируйте движение дракона:
### - Какие переменные влияют на движение дракона на сцене? Укажите эти переменные.
### - Какие переменные влияют на сложность игры на сцене? Укажите эти переменные.
Ход работы:
- На движение дракона влияют 2 переменные. Рандомное значение, нужное для шанса поворота дракона, а также переменная отвечающая за его скорость.
- На сложность игры на сцене влияют следующие параметры: скорость дракона, частота появления яиц, скорость падения яйца, количество щитов, размер оболочки щита.

## Задание 2. Начало работы с шаблоном google-таблицы для балансировки игры
### Ознакомьтесь с презентацией третьей лекции, оцените структуры шаблона таблицы для балансировки. Отметьте, как может быть использован шаблон таблицы для визуализации изменения уровней сложности в игре Dragon Picker?

- Шаблон талицы может очень удобен для того, чтобы наглядно видеть баланс игры перед собой и на графиках. Данную таблицу я буду использовать для себя как пример.

## Задание 3. Задания к работе
## - Задание 1. Предложите вариант изменения найденных переменных для 10 уровней в игре. Визуализируйте изменение уровня сложности в таблице. 
Ход работы:
- Для работы были выбраны следующие переменные: скорость дракона, частота спавна яиц и количество слоёв щита.
- Автоматическое заполнение таблицы:

```py
import gspread
import numpy as np
import math
import matplotlib.pyplot as plt

gc = gspread.service_account(filename='untiry-c709765052a1.json')
sh = gc.open("Workshop3")

def write_indexes():
    for i in range(1, 12):
        sh.sheet1.update_acell("A" + str(i + 1), str(i))

def change_multiplier(multiplier, parameter):
    if parameter == "speed":
        literal = "B"
    else:
        literal = "C"
        multiplier = math.ceil(1 / multiplier * 100) / 100 
    
    current_value = float(sh.sheet1.acell(literal + "2").value)
    values = []
    for i in range(3, 13):
        current_value *= multiplier  # Умножение
        current_value = math.ceil(current_value * 100) / 100  # Округление до 2 знаков после запятой
        formatted_value = str(current_value).replace(".", ",")  # Форматирование
        sh.sheet1.update_acell(literal + str(i), formatted_value)  # Запись в ячейку
        values.append(current_value)
    return values

def shield(addiction):
    shieldcount = int(sh.sheet1.acell("D2").value)
    values = []
    if round(shieldcount*((1-addiction)**(11))) == 0:
        print("shildcount cannot be changed, taken value overly high")
        return
    for i in range(3, 13):
        val = round(shieldcount*((1-addiction)**(i-2)))
        sh.sheet1.update_acell("D" + str(i), str(val))
        values.append(val)
    return values


def plot_graphs(speed_values, eggs_values, shield_values):
    x = np.arange(1, 11)
    
    plt.figure(figsize=(12, 6))
    
    plt.subplot(1, 3, 1)
    plt.plot(x, speed_values, marker='o', linestyle='-', color='b', label='Speed')
    plt.xlabel('Iteration')
    plt.ylabel('Speed')
    plt.title('Speed Growth')
    plt.legend()
    
    plt.subplot(1, 3, 2)
    plt.plot(x, eggs_values, marker='s', linestyle='-', color='g', label='Eggs')
    plt.xlabel('Iteration')
    plt.ylabel('Eggs')
    plt.title('Eggs Growth')
    plt.legend()
    
    plt.subplot(1, 3, 3)
    plt.plot(x, shield_values, marker='^', linestyle='-', color='r', label='Shield')
    plt.xlabel('Iteration')
    plt.ylabel('Shield')
    plt.title('Shield Growth')
    plt.legend()
    
    plt.tight_layout()
    plt.show()

if __name__ == '__main__':
    speed_vals = change_multiplier(1.15, "speed")
    eggs_vals = change_multiplier(1.15, "eggs")
    shield_vals = shield(0.1)
    plot_graphs(speed_vals, eggs_vals, shield_vals)
```
- Таблица описывающая изменения переменных
![image](https://github.com/user-attachments/assets/a787397a-2e56-49d6-93ec-f7decff99a72)
Ссылка на таблицу: https://docs.google.com/spreadsheets/d/1BMK2KGTmmtBwUPiV1-6w1ZGDxzRadyYLHnCjP9yGEOM/edit?gid=0#gid=0


## - Задание 2. Создайте 10 сцен на Unity с изменяющимся уровнем сложности.
Ход работы:
Были созданы 10 сцен, путем копирования основной.

![image](https://github.com/user-attachments/assets/39abdf88-1473-4e21-903c-07f9be2baf0d)


- Для изменения сложности я:
1. Создал пустой объект на сцене и прикрепил к нему скрипт SetSettings, который выгружает из гугл таблицы все данные.

Скрипт компонента:
```C#  
using System;
using System.Collections;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;
using System.Globalization;

public class SetSettings : MonoBehaviour
{
    [SerializeField] private int _sceneNumber;
    private float _dragonSpeed;
    private float _timeEggDrop;

    private int _countShield;

    public float DragonSpeed { get { return _dragonSpeed; } }
    public float TimeEggDrop { get { return _timeEggDrop; } }
    public int CountShield { get { return _countShield; } }

    public event Action CoroutineIsStopped;

        private float StrToFloat(string str)
    {
        str = str.Replace(',', '.');
        float result = float.Parse(str, CultureInfo.InvariantCulture.NumberFormat); ;
        return result;
    }

    void Awake()
    {
        StartCoroutine(GoogleSheets());
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1BMK2KGTmmtBwUPiV1-6w1ZGDxzRadyYLHnCjP9yGEOM/values/Лист1?key=AIzaSyCqQ5r6kFPLTRbWQjD-ugaSgL-DfJRYdqw");


        yield return curentResp.SendWebRequest();
         if (curentResp.isNetworkError || curentResp.isHttpError)
    {
        Debug.LogError("Ошибка при запросе: " + curentResp.error);
    }
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString())[0];
            string lvl = parseJson[0];
            var tryStr = int.TryParse(lvl, out var number);
            if (tryStr == true)
            {
                int intLvl = int.Parse(lvl);
                if (_sceneNumber == intLvl)
                {
                    _dragonSpeed = StrToFloat(parseJson[1]);
                    _timeEggDrop = StrToFloat(parseJson[2]);
                    string strCount = parseJson[3];
                    _countShield = Convert.ToInt32(strCount);
                }
            }
        }
        CoroutineIsStopped?.Invoke();

    }
}
```
2. Редактируем скрипт Dragon Picker, в нем мы должны загрузить данные из таблицы в нужные переменные.

```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;


public class DragonPicker : MonoBehaviour
{
    [SerializeField] private SetSettings _settings;
    public GameObject energyShieldPrefab;
    public int numEnergyShield;
    public float energyShieldBottomY = -6f;
    public float energyShieldRadius = 1.5f;
    
    public List<GameObject> shieldList;

    private void OnEnable()
    {
        _settings.CoroutineIsStopped += UpdateShield;
    }

    void CreateShield()
    {
        shieldList = new List<GameObject>();

        for (int i = 1; i <= numEnergyShield; i++)
        {
            GameObject tShieldGo = Instantiate<GameObject>(energyShieldPrefab);
            tShieldGo.transform.position = new Vector3(0, energyShieldBottomY, 0);
            tShieldGo.transform.localScale = new Vector3(1 * i, 1 * i, 1 * i);
            shieldList.Add(tShieldGo);
        }
    }

    private void UpdateShield()
    {
        numEnergyShield = _settings.CountShield;
        CreateShield();
    }

    public void DragonEggDestroyed(){
        GameObject[] tDragonEggArray = GameObject.FindGameObjectsWithTag("Dragon Egg");
        foreach (GameObject tGO in tDragonEggArray){
            Destroy(tGO);
        }
        int shieldIndex = shieldList.Count - 1;
        GameObject tShieldGo = shieldList[shieldIndex];
        shieldList.RemoveAt(shieldIndex);
        Destroy(tShieldGo);

        if (shieldList.Count == 0){
            SceneManager.LoadScene("_0Scene");
        }
    }
}
```
3. Аналогично со скриптом EnemyDragon.

```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EnemyDragon : MonoBehaviour
{
    public GameObject dragonEggPrefab;
    [SerializeField] private SetSettings _settings;
    public float speed;
    public float timeBetweenEggDrops;
    public float leftRightDistance = 10f;
    public float chanceDirection = 0.1f;

    private void OnEnable()
    {
        _settings.CoroutineIsStopped += UpdateFields;
    }


    void Start()
    {
        Invoke("DropEgg", 2f);
    }

    private void UpdateFields()
    {
        speed = _settings.DragonSpeed;
        timeBetweenEggDrops = _settings.TimeEggDrop;
    }

    void DropEgg(){
        Vector3 myVector = new Vector3(0.0f, 5.0f, 0.0f);
        GameObject egg = Instantiate<GameObject>(dragonEggPrefab);
        egg.transform.position = transform.position + myVector;
        Invoke("DropEgg", timeBetweenEggDrops);
    }

    void Update()
    {
        Vector3 pos = transform.position;
        pos.x += speed * Time.deltaTime;
        transform.position = pos;

        if (pos.x < -leftRightDistance){
            speed = Mathf.Abs(speed);
        }
        else if (pos.x > leftRightDistance){
            speed = -Mathf.Abs(speed);
        }
    }

    private void FixedUpdate() {
        if (Random.value < chanceDirection){
            speed *= -1;
        }
    }
}
```

В результате мы получаем сбалансированный и интересные уровни. (10 уровень):
![image](https://github.com/user-attachments/assets/c5f76b90-f2c2-4160-a314-274c321bb225)


4. Скрипт на python, который я приложил, визуализирует изменения при помощи графиков.

![image](https://github.com/user-attachments/assets/8bf3e67b-6296-48d8-aba9-4b07d57efd2b)


## - Задание 3. Решение в 80+ баллов должно визуализировать данные из google-таблицы, и с помощью Python передавать в проект Unity. В Python данные также должны быть визуализированы.

Все условия 3-его задания были соблюденны.

## Выводы
Я разработал оптимальный баланс изменения сложности для десяти уровней игры Dragon Picker.

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
