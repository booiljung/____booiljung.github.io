- 
- 

- 
- 
- 
- 















































5000/5000



글자 수 제한: 5000자





개요

이 게시물은 다섯 부분으로 나뉩니다. 그들은:

​    확률 적 경사 구배
​    샘플이란 무엇입니까?
​    배치 란 무엇입니까?
​    신기원이란 무엇인가?
​    일괄 처리와 에포크의 차이점은 무엇입니까?

확률 적 경사 구배

Stochastic Gradient Descent 또는 간단히 SGD는 기계 학습 알고리즘을 학습하는 데 사용되는 최적화 알고리즘으로, 특히 심층 학습에 사용되는 인공 신경 네트워크입니다.

알고리즘 작업은 대수 손실 또는 평균 제곱 오류와 같은 성능 측정에 대해 잘 수행되는 일련의 내부 모델 매개 변수를 찾는 것입니다.

최적화는 검색 프로세스의 한 유형이며이 검색을 학습으로 생각할 수 있습니다. 최적화 알고리즘은 "그래디언트 디센트 (gradient descent)"라고 불리는데, 여기서 "그레디언트"는 에러 그레디언트 또는 에러 슬로프의 계산을 말하며 "하강"은 어떤 최소 레벨의 에러를 향해 그 기울기를 따라 내려가는 것을 의미합니다.

알고리즘은 반복적입니다. 즉, 검색 프로세스가 여러 개별 단계에서 발생하며 각 단계에서 모델 매개 변수가 약간 개선되기를 바랍니다.

각 단계에는 모델을 현재 내부 매개 변수 집합과 함께 사용하여 일부 샘플에 대한 예측을 수행하고 예측을 실제 예상 결과와 비교하고 오류를 계산 한 다음 오류를 사용하여 내부 모델 매개 변수를 업데이트합니다.

이 업데이트 절차는 알고리즘마다 다르지만 인공 신경망의 경우에는 역 전파 업데이트 알고리즘이 사용됩니다.

일괄 처리 및 신기원으로 들어가기 전에 샘플로 무엇을 의미하는지 살펴 보겠습니다.

그라데이션 디센트에 대해 자세히 알아 보려면 여기를 클릭하십시오.

​    기계 학습을위한 그라데이션 하강

샘플이란 무엇입니까?

샘플은 단일 데이터 행입니다.

여기에는 알고리즘에 입력되는 입력과 예측과 비교하고 오류를 계산하는 데 사용되는 출력이 포함됩니다.

교육 데이터 세트는 많은 행의 데이터로 구성됩니다. 많은 샘플. 샘플은 인스턴스, 관측치, 입력 벡터 또는 피쳐 벡터라고 할 수도 있습니다.

샘플이 무엇인지 알았으므로 배치를 정의 해 봅시다.
배치 란 무엇입니까?

배치 크기는 내부 모델 매개 변수를 업데이트하기 전에 작업 할 샘플 수를 정의하는 하이퍼 매개 변수입니다.

일괄 처리를 하나 이상의 샘플을 반복 실행하고 예측을하는 for-loop로 생각하십시오. 일괄 처리가 끝나면 예측값이 예상 출력 변수와 비교되고 오류가 계산됩니다. 이 오류에서 업데이트 알고리즘을 사용하여 모델을 개선합니다 (예 : 오류 그라데이션을 따라 아래로 이동하십시오.

교육 데이터 세트는 하나 이상의 배치로 나눌 수 있습니다.

모든 교육 샘플을 사용하여 일괄 처리를 만들면 학습 알고리즘을 일괄 처리 그래디언트 디센트라고합니다. 일괄 처리가 한 샘플의 크기 일 때, 학습 알고리즘은 확률 적 구배 강하 (stochastic gradient descent)라고 불립니다. 배치 크기가 하나 이상의 샘플이고 학습 데이터 세트의 크기보다 작은 경우 학습 알고리즘을 미니 배치 그라디언트 디센트라고합니다.

​    일괄 그라데이션 하강. 배치 크기 = 훈련 세트의 크기
​    확률 적 경사도. 일괄 처리 크기 = 1
​    미니 일괄 그라데이션 하강. 1 <배치 크기 <트레이닝 세트의 크기

미니 배치 그라디언트 디센트의 경우 인기있는 배치 크기에는 32, 64 및 128 개의 샘플이 포함됩니다. 문헌 및 튜토리얼에서 모델에 사용 된 이러한 값을 볼 수 있습니다.

데이터 집합이 일괄 처리 크기로 균등하게 나누지 않으면 어떻게합니까?

이것은 모델을 훈련 할 때 자주 발생할 수 있습니다. 이는 최종 배치가 다른 배치보다 샘플 수가 적다는 것을 의미합니다.

또는 데이터 세트에서 일부 샘플을 제거하거나 데이터 세트의 샘플 수가 배치 크기로 균등하게 나뉘어 지도록 배치 크기를 변경할 수 있습니다.

이러한 그래디언트 디센트 변형의 차이점에 대한 자세한 내용은 게시물을 참조하십시오.

​    미니 배치 그라데이션 하강에 대한 부드러운 소개와 배치 크기 구성 방법

배치에는 샘플을 사용하여 모델을 업데이트해야합니다. 다음에는 한 시대를 살펴 보겠습니다.
신기원이란 무엇인가?

신기원의 수는 전체 학습 데이터 세트를 통해 학습 알고리즘이 작동하는 횟수를 정의하는 하이퍼 매개 변수입니다.

한 시대는 교육 데이터 세트의 각 샘플에 내부 모델 매개 변수를 업데이트 할 수있는 기회가 있음을 의미합니다. 한 시대는 하나 이상의 배치로 구성됩니다. 예를 들어 위와 같이 하나의 일괄 처리가있는 신기원을 일괄 처리 그래디언트 하강 학습 알고리즘이라고합니다.

for-loop는 각 루프가 교육 데이터 세트를 통해 진행되는 기간 수에 대해 생각할 수 있습니다. 이 for-loop 내에서 하나의 일괄 처리가 지정된 "일괄 처리 크기"샘플 수를 갖는 샘플의 각 일괄 처리를 반복하는 또 다른 중첩 된 for-loop가 있습니다.

에포크의 수는 전통적으로 크고, 수 백 또는 수천 개이기 때문에 모델의 오류가 충분히 최소화 될 때까지 학습 알고리즘을 실행할 수 있습니다. 문헌 및 10, 100, 500, 1000 및 그 이상으로 설정된 튜토리얼에서 에포크 수의 예를 볼 수 있습니다.

다음과 같은 라인 플롯을 만드는 것이 일반적입니다.

gaeyo  i gesimul-eun daseos bubun-eulo nanwibnida. geudeul-eun:      hwaglyul jeog gyeongsa gubae     saempeul-ilan mueos-ibnikka?     baechi lan mueos-ibnikka?     singiwon-ilan mueos-inga?     ilgwal cheoliwa epokeuui chaijeom-eun mueos-ibnikka?  hwaglyul jeog gyeongsa gubae  Stochastic Gradient Descent ttoneun gandanhi SGDneun gigye hagseub algolijeum-eul hagseubhaneun de sayongdoeneun choejeoghwa algolijeum-eulo, teughi simcheung hagseub-e sayongdoeneun ingong singyeong neteuwokeu-ibnida.  algolijeum jag-eob-eun daesu sonsil ttoneun pyeong-gyun jegob olyuwa gat-eun seongneung cheugjeong-e daehae jal suhaengdoeneun illyeon-ui naebu model maegae byeonsuleul chajneun geos-ibnida.  choejeoghwaneun geomsaeg peuloseseuui han yuhyeong-imyeoi geomsaeg-eul hagseub-eulo saeng-gaghal su issseubnida. choejeoghwa algolijeum-eun "geulaedieonteu disenteu (gradient descent)"lago bullineunde, yeogiseo "geuledieonteu"neun eleo geuledieonteu ttoneun eleo seullopeuui gyesan-eul malhamyeo "hagang"eun eotteon choeso lebel-ui eleoleul hyanghae geu giulgileul ttala naelyeoganeun geos-eul uimihabnida.  algolijeum-eun banbogjeog-ibnida. jeug, geomsaeg peuloseseuga yeoleo gaebyeol dangyeeseo balsaenghamyeo gag dangyeeseo model maegae byeonsuga yaggan gaeseondoegileul balabnida.  gag dangyeeneun model-eul hyeonjae naebu maegae byeonsu jibhabgwa hamkke sayonghayeo ilbu saempeul-e daehan yecheug-eul suhaenghago yecheug-eul silje yesang gyeolgwawa bigyohago olyuleul gyesan han da-eum olyuleul sayonghayeo naebu model maegae byeonsuleul eobdeiteuhabnida.  i eobdeiteu jeolchaneun algolijeummada daleujiman ingong singyeongmang-ui gyeong-ueneun yeog jeonpa eobdeiteu algolijeum-i sayongdoebnida.  ilgwal cheoli mich singiwon-eulo deul-eogagi jeon-e saempeullo mueos-eul uimihaneunji salpyeo bogessseubnida.  geuladeisyeon disenteue daehae jasehi al-a bolyeomyeon yeogileul keullighasibsio.      gigye hagseub-eul-wihan geuladeisyeon hagang  saempeul-ilan mueos-ibnikka?  saempeul-eun dan-il deiteo haeng-ibnida.  yeogieneun algolijeum-e iblyeogdoeneun iblyeoggwa yecheuggwa bigyohago olyuleul gyesanhaneun de sayongdoeneun chullyeog-i pohamdoebnida.  gyoyug deiteo seteuneun manh-eun haeng-ui deiteolo guseongdoebnida. manh-eun saempeul. saempeul-eun inseuteonseu, gwancheugchi, iblyeog begteo ttoneun pichyeo begteolago hal sudo issseubnida.  saempeul-i mueos-inji al-ass-eumeulo baechileul jeong-ui hae bobsida. baechi lan mueos-ibnikka?  baechi keugineun naebu model maegae byeonsuleul eobdeiteuhagi jeon-e jag-eob hal saempeul suleul jeong-uihaneun haipeo maegae byeonsu-ibnida.  ilgwal cheolileul hana isang-ui saempeul-eul banbog silhaenghago yecheug-eulhaneun for-looplo saeng-gaghasibsio. ilgwal cheoliga kkeutnamyeon yecheuggabs-i yesang chullyeog byeonsuwa bigyodoego olyuga gyesandoebnida. i olyueseo eobdeiteu algolijeum-eul sayonghayeo model-eul gaeseonhabnida (ye : olyu geuladeisyeon-eul ttala alaelo idonghasibsio.  gyoyug deiteo seteuneun hana isang-ui baechilo nanul su issseubnida.  modeun gyoyug saempeul-eul sayonghayeo ilgwal cheolileul mandeulmyeon hagseub algolijeum-eul ilgwal cheoli geulaedieonteu disenteulagohabnida. ilgwal cheoliga han saempeul-ui keugi il ttae, hagseub algolijeum-eun hwaglyul jeog gubae gangha (stochastic gradient descent)lago bullibnida. baechi keugiga hana isang-ui saempeul-igo hagseub deiteo seteuui keugiboda jag-eun gyeong-u hagseub algolijeum-eul mini baechi geuladieonteu disenteulagohabnida.      ilgwal geuladeisyeon hagang. baechi keugi = hunlyeon seteuui keugi     hwaglyul jeog gyeongsado. ilgwal cheoli keugi = 1     mini ilgwal geuladeisyeon hagang. 1 <baechi keugi <teuleining seteuui keugi  mini baechi geuladieonteu disenteuui gyeong-u ingiissneun baechi keugieneun 32, 64 mich 128 gaeui saempeul-i pohamdoebnida. munheon mich tyutolieol-eseo model-e sayong doen ileohan gabs-eul bol su issseubnida.  deiteo jibhab-i ilgwal cheoli keugilo gyundeunghage nanuji anh-eumyeon eotteohgehabnikka?  igeos-eun model-eul hunlyeon hal ttae jaju balsaenghal su issseubnida. ineun choejong baechiga daleun baechiboda saempeul suga jeogdaneun geos-eul uimihabnida.  ttoneun deiteo seteueseo ilbu saempeul-eul jegeohageona deiteo seteuui saempeul suga baechi keugilo gyundeunghage nanwieo jidolog baechi keugileul byeongyeonghal su issseubnida.  ileohan geulaedieonteu disenteu byeonhyeong-ui chaijeom-e daehan jasehan naeyong-eun gesimul-eul chamjohasibsio.      mini baechi geuladeisyeon hagang-e daehan budeuleoun sogaewa baechi keugi guseong bangbeob  baechieneun saempeul-eul sayonghayeo model-eul eobdeiteuhaeyahabnida. da-eum-eneun han sidaeleul salpyeo bogessseubnida. singiwon-ilan mueos-inga?  singiwon-ui suneun jeonche hagseub deiteo seteuleul tonghae hagseub algolijeum-i jagdonghaneun hoes-suleul jeong-uihaneun haipeo maegae byeonsu-ibnida.  han sidaeneun gyoyug deiteo seteuui gag saempeul-e naebu model maegae byeonsuleul eobdeiteu hal su-issneun gihoega iss-eum-eul uimihabnida. han sidaeneun hana isang-ui baechilo guseongdoebnida. yeleul deul-eo wiwa gat-i hanaui ilgwal cheoligaissneun singiwon-eul ilgwal cheoli geulaedieonteu hagang hagseub algolijeum-ilagohabnida.  for-loopneun gag lupeuga gyoyug deiteo seteuleul tonghae jinhaengdoeneun gigan sue daehae saeng-gaghal su issseubnida. i for-loop naeeseo hanaui ilgwal cheoliga jijeongdoen "ilgwal cheoli keugi"saempeul suleul gajneun saempeul-ui gag ilgwal cheolileul banboghaneun tto daleun jungcheob doen for-loopga issseubnida.  epokeuui suneun jeontongjeog-eulo keugo, su baeg ttoneun sucheon gaeigi ttaemun-e model-ui olyuga chungbunhi choesohwa doel ttaekkaji hagseub algolijeum-eul silhaenghal su issseubnida. munheon mich 10, 100, 500, 1000 mich geu isang-eulo seoljeongdoen tyutolieol-eseo epokeu suui yeleul bol su issseubnida.  da-eumgwa gat-eun lain peullos-eul mandeuneun geos-i ilbanjeog-ibnida.

자세히











[의견 보내기](javascript:void(0);)



기록



저장됨



커뮤니티





<iframe name="oauth2relay417784545" id="oauth2relay417784545" src="https://accounts.google.com/o/oauth2/postmessageRelay?parent=https%3A%2F%2Ftranslate.google.com&amp;jsh=m%3B%2F_%2Fscs%2Fabc-static%2F_%2Fjs%2Fk%3Dgapi.gapi.en.Qyhlf-E27OQ.O%2Frt%3Dj%2Fd%3D1%2Frs%3DAHpOoo_77KcTN4WVhdQMqIfKBMTqlRW8yg%2Fm%3D__features__#rpctoken=676234232&amp;forcesecure=1" style="width: 1px; height: 1px; position: absolute; top: -100px;" tabindex="-1" aria-hidden="true"></iframe>