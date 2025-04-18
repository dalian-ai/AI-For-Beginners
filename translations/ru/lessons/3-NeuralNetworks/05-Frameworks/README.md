# Фреймворки нейронных сетей

Как мы уже узнали, для эффективного обучения нейронных сетей нам нужно сделать две вещи:

* Операции с тензорами, например, умножение, сложение и вычисление некоторых функций, таких как сигмоид или софтмакс
* Вычисление градиентов всех выражений для выполнения оптимизации с помощью градиентного спуска

## [Предварительный тест](https://red-field-0a6ddfd03.1.azurestaticapps.net/quiz/105)

Хотя библиотека `numpy` может выполнять первую часть, нам нужен механизм для вычисления градиентов. В [нашем фреймворке](../../../../../lessons/3-NeuralNetworks/04-OwnFramework/OwnFramework.ipynb), который мы разработали в предыдущем разделе, нам пришлось вручную программировать все функции производных внутри метода `backward`, который выполняет обратное распространение. В идеале фреймворк должен предоставлять возможность вычислять градиенты *любого выражения*, которое мы можем определить.

Еще одна важная вещь — это возможность выполнять вычисления на GPU или любых других специализированных вычислительных устройствах, таких как [TPU](https://en.wikipedia.org/wiki/Tensor_Processing_Unit). Обучение глубоких нейронных сетей требует *много* вычислений, и возможность параллелизировать эти вычисления на GPU очень важна.

> ✅ Термин «параллелизовать» означает распределять вычисления между несколькими устройствами.

В настоящее время двумя наиболее популярными нейронными фреймворками являются: [TensorFlow](http://TensorFlow.org) и [PyTorch](https://pytorch.org/). Оба предоставляют низкоуровневый API для работы с тензорами как на CPU, так и на GPU. Сверху низкоуровневого API также существуют высокоуровневые API, называемые [Keras](https://keras.io/) и [PyTorch Lightning](https://pytorchlightning.ai/) соответственно.

Низкоуровневый API | [TensorFlow](http://TensorFlow.org) | [PyTorch](https://pytorch.org/)
------------------|----------------------------------|--------------------------------
Высокоуровневый API| [Keras](https://keras.io/) | [PyTorch Lightning](https://pytorchlightning.ai/)

**Низкоуровневые API** в обоих фреймворках позволяют строить так называемые **вычислительные графы**. Этот граф определяет, как вычислить выходные данные (обычно функцию потерь) с заданными входными параметрами и может быть отправлен на вычисление на GPU, если он доступен. Существуют функции для дифференцирования этого вычислительного графа и вычисления градиентов, которые затем могут быть использованы для оптимизации параметров модели.

**Высокоуровневые API** рассматривают нейронные сети как **последовательность слоев** и значительно упрощают построение большинства нейронных сетей. Обучение модели обычно требует подготовки данных, а затем вызова функции `fit` для выполнения работы.

Высокоуровневый API позволяет быстро строить типичные нейронные сети, не беспокоясь о множестве деталей. В то же время низкоуровневый API предлагает гораздо больший контроль над процессом обучения, и, следовательно, они часто используются в исследованиях, когда вы работаете с новыми архитектурами нейронных сетей.

Также важно понимать, что вы можете использовать оба API вместе, например, вы можете разработать свою собственную архитектуру слоя сети, используя низкоуровневый API, а затем использовать ее внутри более крупной сети, построенной и обученной с помощью высокоуровневого API. Или вы можете определить сеть, используя высокоуровневый API как последовательность слоев, а затем использовать свой собственный низкоуровневый цикл обучения для выполнения оптимизации. Оба API используют одни и те же основные концепции и предназначены для хорошей совместной работы.

## Обучение

В этом курсе мы предлагаем большинство материалов как для PyTorch, так и для TensorFlow. Вы можете выбрать свой предпочтительный фреймворк и проходить только соответствующие блокноты. Если вы не уверены, какой фреймворк выбрать, прочитайте некоторые обсуждения в интернете о **PyTorch против TensorFlow**. Вы также можете ознакомиться с обоими фреймворками, чтобы лучше понять их.

Где это возможно, мы будем использовать высокоуровневые API для простоты. Однако мы считаем важным понять, как работают нейронные сети с нуля, поэтому в начале мы начнем с работы с низкоуровневым API и тензорами. Однако, если вы хотите быстро приступить к делу и не хотите тратить много времени на изучение этих деталей, вы можете пропустить их и сразу перейти к блокнотам с высокоуровневым API.

## ✍️ Упражнения: Фреймворки

Продолжайте свое обучение в следующих блокнотах:

Низкоуровневый API | [Блокнот TensorFlow+Keras](../../../../../lessons/3-NeuralNetworks/05-Frameworks/IntroKerasTF.ipynb) | [PyTorch](../../../../../lessons/3-NeuralNetworks/05-Frameworks/IntroPyTorch.ipynb)
------------------|------------------------------------|--------------------------------
Высокоуровневый API| [Keras](../../../../../lessons/3-NeuralNetworks/05-Frameworks/IntroKeras.ipynb) | *PyTorch Lightning*

После освоения фреймворков давайте подытожим понятие переобучения.

# Переобучение

Переобучение — это чрезвычайно важная концепция в машинном обучении, и очень важно правильно ее понять!

Рассмотрим следующую задачу аппроксимации 5 точек (представленных `x` на графиках ниже):

![linear](../../../../../translated_images/overfit1.f24b71c6f652e59e6bed7245ffbeaecc3ba320e16e2221f6832b432052c4da43.ru.jpg) | ![overfit](../../../../../translated_images/overfit2.131f5800ae10ca5e41d12a411f5f705d9ee38b1b10916f284b787028dd55cc1c.ru.jpg)
-------------------------|--------------------------
**Линейная модель, 2 параметра** | **Нелинейная модель, 7 параметров**
Ошибка обучения = 5.3 | Ошибка валидации = 0
Ошибка валидации = 5.1 | Ошибка валидации = 20

* Слева мы видим хорошую линейную аппроксимацию. Поскольку количество параметров адекватно, модель правильно улавливает идею распределения точек.
* Справа модель слишком мощная. Поскольку у нас всего 5 точек, а модель имеет 7 параметров, она может настроиться таким образом, чтобы проходить через все точки, что делает ошибку обучения равной 0. Однако это мешает модели понять правильный шаблон данных, и, следовательно, ошибка валидации очень высока.

Очень важно найти правильный баланс между сложностью модели (количеством параметров) и количеством обучающих образцов.

## Почему происходит переобучение

  * Недостаточно обучающих данных
  * Слишком мощная модель
  * Слишком много шума в входных данных

## Как обнаружить переобучение

Как видно из графика выше, переобучение можно обнаружить по очень низкой ошибке обучения и высокой ошибке валидации. Обычно во время обучения мы видим, как ошибки обучения и валидации начинают снижаться, а затем в какой-то момент ошибка валидации может перестать снижаться и начать расти. Это будет признаком переобучения и индикатором того, что, вероятно, стоит остановить обучение в этот момент (или, по крайней мере, сделать снимок модели).

![overfitting](../../../../../translated_images/Overfitting.408ad91cd90b4371d0a81f4287e1409c359751adeb1ae450332af50e84f08c3e.ru.png)

## Как предотвратить переобучение

Если вы видите, что происходит переобучение, вы можете сделать одно из следующего:

 * Увеличить объем обучающих данных
 * Уменьшить сложность модели
 * Использовать какую-либо [технику регуляризации](../../4-ComputerVision/08-TransferLearning/TrainingTricks.md), такую как [Dropout](../../4-ComputerVision/08-TransferLearning/TrainingTricks.md#Dropout), которую мы рассмотрим позже.

## Переобучение и компромисс смещения-дисперсии

Переобучение на самом деле является случаем более общей проблемы в статистике, называемой [компромиссом смещения-дисперсии](https://en.wikipedia.org/wiki/Bias%E2%80%93variance_tradeoff). Если мы рассмотрим возможные источники ошибок в нашей модели, мы можем увидеть два типа ошибок:

* **Ошибки смещения** возникают из-за того, что наш алгоритм не может правильно захватить взаимосвязь между обучающими данными. Это может быть результатом того, что наша модель недостаточно мощная (**недообучение**).
* **Ошибки дисперсии**, которые возникают из-за того, что модель аппроксимирует шум во входных данных вместо значимой взаимосвязи (**переобучение**).

Во время обучения ошибка смещения уменьшается (по мере того, как наша модель учится аппроксимировать данные), а ошибка дисперсии увеличивается. Важно остановить обучение - либо вручную (когда мы обнаруживаем переобучение), либо автоматически (вводя регуляризацию) - чтобы предотвратить переобучение.

## Заключение

В этом уроке вы узнали о различиях между различными API для двух самых популярных фреймворков ИИ, TensorFlow и PyTorch. Кроме того, вы узнали о очень важной теме — переобучении.

## 🚀 Задача

В прилагаемых блокнотах вы найдете «задачи» внизу; пройдите через блокноты и выполните задания.

## [Посттест](https://red-field-0a6ddfd03.1.azurestaticapps.net/quiz/205)

## Обзор и самостоятельное изучение

Проведите исследование по следующим темам:

- TensorFlow
- PyTorch
- Переобучение

Задайте себе следующие вопросы:

- В чем разница между TensorFlow и PyTorch?
- В чем разница между переобучением и недообучением?

## [Задание](lab/README.md)

В этой лабораторной работе вам предложено решить две задачи классификации, используя однослойные и многослойные полностью связанные сети с использованием PyTorch или TensorFlow.

* [Инструкции](lab/README.md)
* [Блокнот](../../../../../lessons/3-NeuralNetworks/05-Frameworks/lab/LabFrameworks.ipynb)

**Отказ от ответственности**:  
Этот документ был переведен с использованием услуг машинного перевода на основе ИИ. Хотя мы стремимся к точности, пожалуйста, имейте в виду, что автоматические переводы могут содержать ошибки или неточности. Оригинальный документ на родном языке должен считаться авторитетным источником. Для критически важной информации рекомендуется профессиональный человеческий перевод. Мы не несем ответственности за любые недоразумения или неправильные толкования, возникающие в результате использования этого перевода.