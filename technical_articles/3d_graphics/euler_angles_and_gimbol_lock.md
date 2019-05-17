[Up](index.md)

# 오일러 회전과 짐벌락(Euler angles gimbol lock)

<iframe allowfullscreen="" frameborder="0" height="315" src="http://www.youtube.com/embed/zc8b2Jo7mno" width="420"></iframe>

Gimbal lock (짐벌락)은 오일러 회전을 할때 발생 합니다. &nbsp;컴퓨터의 연산시 오일러 회전은 x, y, z나 y, z, x등 순차적으로 할 수 밖에 없으므로 나중에 연산은 먼저의 연산에 종속됩니다.

A 회전축이 회전하여 B 회전축과 일치하고 나서, B회전축이 회전하고, 다시 A회전축이 회전하려고 할때 회전이 A회전축이 B회전축과 일치하여 회전이 불가능해 집니다.

이를 예방하기 위해 Quaternion(사원수)를 사용합니다. &nbsp;쿼터니언은 세개의 축이 동시에 연산되기때문에 짐벌락이 발생하지 않습니다. &nbsp;그런데 쿼터니언은 인간의 인식 세계에서 이해가 어렵다는 문제가 있습니다.