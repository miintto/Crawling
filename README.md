네이버 영화 평점 Crawling
==========


# 1. 사이트 구조 파악

영화 '신과 함께'의 평점을 크롤링한다고 가정해보자,
가장 먼저 필요한건 크롤링하고자 하는 페이지의 url이다.

네이버 영화 사이트의 url은 다음과 같다.

https://movie.naver.com/movie/bi/mi/basic.nhn?code=85579

>여기서 제일 마지막에 붙은 85579가 영화의 고유번호라고 생각하면 쉽다.
저 번호를 변경해주면 다른 영화의 페이지로 이동하게 된다.
> ex) '라라랜드' 는 134963, '범죄도시'는 161242 
>
>처음엔 시간순으로 번호를 붙이지 않았나 예상했는데, 딱히 규칙은 없는둣하다.

하지만 영화의 평점에 대한 정보는 이곳에 들어가있지 않고 이 페이지 안에 있는 또 다른 프레임에 속에 들어있다.

위의 주소에서도 클릭 몇번으로도 평점을 확인할 수 있지만 가져올 평점에 대한 정보가 담겨있는 곳은 아래 url이다.

https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code=85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page=1

위 url로 들어가면 평점 창으로 직접 들어갈 수 있다.
마찬가지로 가운데 들어있는 85579가 영화 번호이고, 마지막에 붙는 숫자는 페이지 번호다.

고로 영화 번호와 페이지 번호 두가지를 적절히 바꿔주면 웬만한 영화 평점은 뽑아올수 있는 구조다.

# 2. html 소스 분석

자, 이제 평점과 코멘트 내용을 가져와야 한다.
해당 창에서 마우스 오른쪽 버튼을 눌러 '페이지 소스 보기'를 클릭하면 해당 페이지의 소스를 볼 수 있다.

다음과 같은 소스가 쭈욱 나온다.

>\<!DOCTYPE html>
>\<html lang="ko">
>\<head>
>\<meta charset="utf-8">
>\<meta http-equiv="X-UA-Compatible" content="IE=edge">
>\<title>네이버 영화\</title>
>\
>\<link rel="shortcut icon" href="[https://ssl.pstatic.net/static/m/movie/icons/naver_movie_favicon.ico](https://ssl.pstatic.net/static/m/movie/icons/naver_movie_favicon.ico)" type="image/x-icon">
>		
>\<link rel="stylesheet" type="text/css" href="[/css/common.css?20180329144629](https://movie.naver.com/css/common.css?20180329144629)" />
>	
>\<link rel="stylesheet" type="text/css" href="[/css/movie_tablet.css?20180329144629](https://movie.naver.com/css/movie_tablet.css?20180329144629)" />
>\<link rel="stylesheet" type="text/css" href="[/css/movie_end.css?20180329144629](https://movie.naver.com/css/movie_end.css?20180329144629)" />
	...

>## 이곳에서 내가 원하는 텍스트가 어느 위치에 있는지 파악하는게 매우 중요하다!!

기본적인 구조만 보자면
여는 태그 <>, 닫는 태그 </>가 반복되면서 프레임이 구성되고 있다.
필자도 html에 무지하지만 이정도 개념만 알고있어도 크게 문제될건 없다고 본다.

좀더 스크롤을 내려보면 영화 평점이 담겨있는 부분을 발견할 수 있다.

>\<li>
>\<div class="star_score">
	>\<span class="st_off"><span class="st_on" style="width:100.0%"></span>>\</span><em>10</em>
	>\</div>
	>\<div class="score_reple">
	>\<p>\<span class="ico_best">BEST</span><span class="ico_viewer">관람객</span>이정재가 홍보 따라다닌 이유를 알겠음 ㅋㅋㅋ 그리고 관심병사 연기 도랐다 \</p>
	>\<dl>
	>\<dt>
	>\<em>
	>\<a href="[#](https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code=85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page=1#)" onclick="javascript:showPointListByNid(13508573, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
	>\<span>붕어졍(3sec****)\</span>
	>\</a>
	>\</em>
	>\<em>2017.12.20 11:53\</em>
	>\</dt>
	>\<dd>
	>\<a href="[#](https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code=85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page=1#)" onclick="parent.clickcr(this, 'ara.report', '', '', event); common.report('false','3sec****', 'pK+X6f3pAT2fXpTe+JMS5Ok5AVIqFU1mLGIYRbfxRI8=', '이정재가 홍보 따라다닌 이유를 알겠음 ㅋㅋㅋ 그리고 관심병사 연기 도랐다 ', '13508573', 'point_after', true);return false;" class="go_report2">\<em>신고\</em>\</a>
	>\</dd>
	>\</dl>
	>\</div>
	>\<div class="btn_area">
	>\<a class="_sympathyButton" href="[#](https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code=85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page=1#)" onclick="parent.clickcr(this, 'ara.sym', '', '', event);">\<span class="blind">공감\</span>\</a>\<strong>\<span class="sympathy_13508573 count">8994\</span>\</strong>
	>\<a class="_notSympathyButton" href="[#](https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code=85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page=1#)" onclick="parent.clickcr(this, 'ara.opp', '', '', event);">\<span class="blind">비공감\</span>\</a>\<strong>\<span class="notSympathy_13508573 count v2">1972\</span>\</strong>
	>\</div>
	>\</li>

구조를 잘 살펴보면 \<li>과 \</li> 사이에 코멘트와 평점이 담겨있음을 알 수 있다.

# 3. 크롤링 (feat. Python 3)
## 3.1 코드 구조 분석

이젠 파이썬을 이용해 크롤링을 해보도록 하자.

우선 필요한 패키지는 다음과 같다.

	from bs4 import BeautifulSoup
	import urllib.request as req
	import math

페이지 url을 다음과 같이 쪼개어 준다.

	url1 = 'https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code='
	num = 85579
	url2 = '&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page='
	page = 1
	
	url1+str(num)+url2+str(page)

num이 영화의 고유 번호이고, page가 페이지 번호가 된다.
이때 네 개를 모두 조합한 url이 실제 크롤링할 페이지의 주소가 된다.

이제 해당 url에서 html소스를 뽑아올 차례이다.

	response = req.urlopen(url1+str(num)+url2+str(page))
	soup = BeautifulSoup(response, 'html.parser')

urlopen()은 해당 페이지에 대한 정보를 가져오는 역할을 한다.
따라서 response에는 지정한 주소의 데이터가 들어있는데, BeautifulSoup()로 안에 담긴 페이지 소스를 뽑아올 수 있다.

>soup를 직접 출력해보면 위에서 확인했었던 페이지 소스와 동일한 소스가 출력됨을 알 수 있다.

이제 저 방대한 양의 소스에서 필요한 부분만 가져오도록 하자.

소스를 잘 살펴보면 평점들은 \<li>\</li>안에 각각 나누어 담겨있고  또 \<li>\</li>들은 \<div class="score_result">\</div>안에 담겨있음을 알 수 있다.

	<div class="score_result">
		<li>
			평점1
		</li>
		<li>
			평점2
		</li>
		<li>
			평점3
		</li>
			....
	</div>

>간단히 살펴보면 대략 이런식의 구조라고 할수 있다.

이제 저 부분에서 \<div>\</div>사이에 있는 부분만 가져올거다.
class="score_result" 는 해당 \<div>의 이름정도로 알아주면 편하다.

	score_result = soup.find('div', class_ = 'score_result')
	lis = score_result.find_all('li')

find()는 조건에 부합하는 부분을 찾아주는 함수다. 안의 내용은 'div' 중에서 class가 score_result인 부분만을 찾도록 하는 명령이다.
규칙만 알고 있으면 다용도로 변형이 가능하다.

find_all()은 주어진 조건에 알맞은 부분을 벡터형태로 찾아준다.

결과적으로 보면 score_result안에는 \<div class="score_result">\</div>안에 담겨있는 모든 코멘트들이 담겨있고, lis안에는 평점을 포함하고 있는 \<li>\</li>들이 한 도막씩 저장되어 있다.

>첫번째 도막 lis[0]을 출력해보면 html 소스 분석에서 보여진  \<li>\</li>안의 내용과 동일한 소스가 출력되게 된다.

>\<li>
>\<div class="star_score">
>\<span class="st_off">\<span class="st_on" style="width:100.0%">\</span>\</span>\<em>10\</em>
>\</div>
>\<div class="score_reple">
>\<p>\<span class="ico_best">BEST\</span>\<span class="ico_viewer">관람객\</span>이정재가 홍보 따라다닌 이유를 알겠음 ㅋㅋㅋ 그리고 관심병사 연기 도랐다 \</p>
>...

이젠 해당 소스에서 텍스트만 출력해보자.

평점은 \<div class="star_score"> 안의 \<em>\</em>사이에 위치한다.
긴 문장이 아니라서 발견하기 힘들수도 있다....
또한 코멘트는 \<div class="score_reple">안의 \<p>\</p> 사이에 위치한다.

다음 코드를 이용해 제일 처음 평점만 가져와보자.

	score = lis[0].select_one('div.star_score > em')
    reple = lis[0].select_one('div.score_reple > p')
select_one()의 구조도 보면 알겠지만 해당 div 안에 em이나 p 안의 부분을 불러오는 명령이다.
하지만 이 상태로 출력을 하게 되면

>\<em>10\</em>
>\<p>\<span class="ico_best">BEST\</span>\<span class="ico_viewer">관람객\</span>이정재가 홍보 따라다닌 이유를 알겠음 ㅋㅋㅋ 그리고 관심병사 연기 도랐다  \</p>

아직 태그가 제거되지 않아 이렇게 나타나게 된다.
이럴땐 get_text()함수를 이용하여 텍스트만 뽑아줄 수 있다.

적용해보면 다음과 같다.

    print('평점 :',  score.get_text())
    print('리뷰 :', reple.get_text())
>10
>BEST관람객이정재가 홍보 따라다닌 이유를 알겠음 ㅋㅋㅋ 그리고 관심병사 연기 도랐다

이런식으로 가능하다.

이를 좀더 응용하면

	url1 = 'https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code='
	num = 85579
	url2 = '&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page='
	page = 1
	
	response = req.urlopen(url1+str(num)+url2+str(page))
	print(response)soup = BeautifulSoup(response, 'html.parser')
	score_result = soup.find('div', class_ = 'score_result')
	lis = score_result.find_all('li')
	
	for li in lis:
	    score = li.select_one('div.star_score > em')
	    reple = li.select_one('div.score_reple > p')
	    print('평점 :', score.get_text())
	    print('리뷰 :', reple.get_text())

>평점 : 10
>리뷰 : BEST관람객이정재가 홍보 따라다닌 이유를 알겠음 ㅋㅋㅋ 그리고 관심병사 연기 도랐다  
>평점 : 10
>리뷰 : BEST관람객나 죽어서 지옥가면 어떡하냐 ㄷㄷㄷ 앞으로 착하게 살아야지  
>평점 : 10
>리뷰 : BEST관람객남자 셋이서 안 울라고…주먹으로 입틀막함 ㅋㅋㅋㅋ  
>평점 : 10
>리뷰 : BEST관람객여보, 이번 겨울에… 부모님댁에 밥솥 놔드려야겠어요  
>평점 : 10
>리뷰 : BEST관람객가족이랑 봤는데… 나… 우리 아빠 우는 거 처음 봤음 ㅜㅜㅜㅜ  
>평점 : 8
>리뷰 : 관람객오늘도 열심히 사는 대한민국의 자홍,수홍씨들을 응원합니다 ㅠㅠ  
>평점 : 10
>리뷰 : 야 이거 생각한 것 보다 잘 나왔잖아 ?  
>평점 : 10
>리뷰 : 관람객오늘 화장 안하고 신과함께를 본 것은 신의한수였음.  
>평점 : 10
>리뷰 : 원작팬이에요. 솔직히 기대 내려놓고 봤지만 볼거리, 스토리, 캐릭터 케미 모두 기대 이상이었어요. 이정도 영화 없다고 봅니다. 많이 감동 받았어요  
>평점 : 10
>리뷰 : 관람 내내 지옥행 롤러코스터 타고 온 기분이었음 ㅋㅋㅋㅋ

와 같이 크롤링이 가능하다.

