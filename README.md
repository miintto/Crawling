[네이버 영화 평점 크롤링 (feat. Python 3)]
==========


# 1. 사이트 구조 파악
## 1.1 URL 분석

영화 '신과 함께'의 평점을 크롤링한다고 가정해보자. 가장 먼저 필요한건 **크롤링하고자 하는 페이지의 url**이다. 웹페이지 크롤링의 원리가 해당 페이지의 html 소스에서 원하는 내용을 긁어오는 방식이기 때문에 url의 규칙을 잘 파악해두면 응용하는데 꽤 많은 도움이 된다.

영화 신과함께 평점 창의 url은 다음과 같다.

https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code=85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page=1

>중간에 붙은 85579가 영화의 고유번호라고 생각하면 쉽다.
>저 번호를 변경해주면 다른 영화의 페이지로 이동하게 된다.
> ex) '라라랜드' 는 134963, '범죄도시'는 161242 같은 식으로...
>개인적으로는 시간순으로 번호를 붙이지 않았나 생각했는데, 딱히 규칙은 없는둣하다.
>
>제일 마지막에 있는 번호는 페이지 번호다.
>한 페이지당 평점 10개씩 있고, for문으로 저걸 넘겨주는 코드를 짜면 한번에 대량의 평점을 가져올 수 있다.

네이버 영화 사이트의 구조를 살펴보면 중심이 되는 큰 틀 안에 여러 프레임이 들어있는 형태로 구성되어있는데 각각 url이 분리되어 있다.
따라서 메인 창에서도 클릭 몇번으로 평점을 확인할 수 있지만, 정작 평점에 대한 데이터는 다른 프레임 안에 저장되어 있다.
그 프레임의 주소가 위와 같다.
또한 주어진 url로 접속하게 되면 평점 창으로만 들어가게 된다.

----------

## 1.2 html 소스 분석

자, 이제 평점과 코멘트 내용을 가져와야 한다.
해당 창에서 마우스 오른쪽 버튼을 눌러 '페이지 소스 보기'를 클릭하면 해당 페이지의 소스를 볼 수 있다.
메인 창에서 불러오고 싶다면 '프레임 소스 보기'를 클릭하면 된다.

다음과 같은 소스가 쭈욱 나온다.

	<!DOCTYPE html>
	<html lang="ko">
	<head>
	<meta charset="utf-8">
	<meta http-equiv="X-UA-Compatible" content="IE=edge">
	<title>네이버 영화</title>


		<link rel="shortcut icon" href="[https://ssl.pstatic.net/static/m/movie/icons/naver_movie_favicon.ico](https://ssl.pstatic.net/static/m/movie/icons/naver_movie_favicon.ico)" type="image/x-icon">

	<link rel="stylesheet" type="text/css" href="[/css/common.css?20180329144629](https://movie.naver.com/css/common.css?20180329144629)" />
	<link rel="stylesheet" type="text/css" href="[/css/movie_tablet.css?20180329144629](https://movie.naver.com/css/movie_tablet.css?20180329144629)" />
	<link rel="stylesheet" type="text/css" href="[/css/movie_end.css?20180329144629](https://movie.naver.com/css/movie_end.css?20180329144629)" />
	...

필자는 html에 대해 무지하지만 html 구조가 여는 태그 <>, 닫는 태그 </>가 반복되면서 구성된다는건 익히 들어 알고 있다.
이정도 개념만 알고있어도 크롤링엔 크게 문제될건 없다고 본다.

좀 더 스크롤을 내려 영화 평점이 담겨있는 부분을 확인하자.

	....
	<li>
		<div class="star_score">
			<span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>
		</div>
		<div class="score_reple">
			<p><span class="ico_best">BEST</span><span class="ico_viewer">관람객</span>이정재가 홍보 따라다닌 이유를 알겠음 ㅋㅋㅋ 그리고 관심병사 연기 도랐다 </p>
			<dl>
				<dt>
					<em>
						<a href="[#](https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code=85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false#)" onclick="javascript:showPointListByNid(13508573, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
							<span>붕어졍(3sec****)</span>
						</a>
					</em>
					<em>2017.12.20 11:53</em>
				</dt>
				<dd>
					<a href="[#](https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code=85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false#)" onclick="parent.clickcr(this, 'ara.report', '', '', event); common.report('false','3sec****', 'JlVoh6NIPxi5+w8ytWXl30K2VUTR11eNDcHtNAObrKE=', '이정재가 홍보 따라다닌 이유를 알겠음 ㅋㅋㅋ 그리고 관심병사 연기 도랐다 ', '13508573', 'point_after', false);return false;" class="go_report2"><em>신고</em></a>
				</dd>
			</dl>
		</div>
		<div class="btn_area">
			<a class="_sympathyButton" href="[#](https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code=85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false#)" onclick="parent.clickcr(this, 'ara.sym', '', '', event);"><span class="blind">공감</span></a><strong><span class="sympathy_13508573 count">8996</span></strong>
			<a class="_notSympathyButton" href="[#](https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code=85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false#)" onclick="parent.clickcr(this, 'ara.opp', '', '', event);"><span class="blind">비공감</span></a><strong><span class="notSympathy_13508573 count v2">1973</span></strong>
		</div>
	</li>
	...

\<li>과 \</li> 사이에 코멘트와 평점이 담겨있음을 알 수 있다.
더 자세히 살펴보면 평점은 \<div class="star_score"> 안의 \<em>\</em>사이에 위치한다.
긴 문장이 아니라서 발견하기 힘들수도 있다....
또한 코멘트는 \<div class="score_reple">안의 \<p>\</p> 사이에 위치한다.
**이런식으로 내가 원하는 텍스트가 어느 위치에 있는지 파악하는게 매우 중요하다!**

----------
----------

# 2. 크롤링
## 2.1 코드 구조 분석

이젠 파이썬을 이용해 크롤링을 해보도록 하자.

우선 필요한 라이브러리는 다음과 같다.

	from bs4 import BeautifulSoup
	import urllib.request as req
	import math

**BeautifulSoup**는 html 데이터를 파싱 및 탐색하는 함수를 포함하고 있다.
**urllib**는 웹에서 html 소스를 불러올때 사용된다.
둘 다 필요하니 꼭 설치해두자.

이제 해당 url에서 html소스를 뽑아올 차례이다.

	url = 'https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code=85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page=1'
	response = req.urlopen(url)
	soup = BeautifulSoup(response, 'html.parser')

먼저 url변수에 영화 평점 페이지의 주소를 넣어주었다.
urlopen()은 해당 페이지에 대한 정보를 가져오는 역할을 한다.
따라서 response에는 지정한 주소의 데이터가 들어있는데, BeautifulSoup()로 html데이터를 파싱하여 안에 담긴 페이지 소스를 뽑아올 수 있다.
**용어가 어려우면 크롤링하고자 하는 html 페이지의 소스를 가져오는 코드라고 생각하면 편하다.**

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

>간단히 설명하자면 대략 이런식의 구조라고 할수 있다.

이제 저 부분에서 \<div>\</div>사이에 있는 부분만 가져오려고 한다.
class="score_result" 는 해당 \<div>의 이름 정도로 생각하면 편하다.

	score_result = soup.find('div', class_ = 'score_result')
	lis = score_result.find_all('li')

find()는 조건에 부합하는 부분을 찾아주는 함수다. 안의 내용은 'div' 중에서 class가 score_result인 부분만을 찾도록 하는 명령이다.
규칙만 알고 있으면 다용도로 변형이 가능하다.
find_all()은 주어진 조건에 알맞은 부분을 모두 찾아 벡터 형태로 묶어 내보내준다.

결과적으로 보면 score_result안에는 \<div class="score_result">\</div>안에 담겨있는 모든 코멘트들이 담겨있고, lis안에는 평점을 포함하고 있는 \<li>\</li>들이 한 도막씩 벡터형태로 저장되어 있다. (한 페이지에 평점이 10개씩 있으니 10개의 \<li>\</li>단위가 나열되어 었는 셈이다.)

>lis = [\<li> 평점1 \</li>, \<li> 평접2 \</li>, \<li> 평점3 \</li>, ... , \<li> 평점10 \</li>]
>이런 식으로 저장되어있다. 
>위에서 \<li> 평점 \</li>라고 간략히 썼지만 사실은 다양한 태그로 조합된 복잡한 소스다.

이젠 해당 소스에서 텍스트만 출력해보자.
위에서 확인했듯이 각각 \<div class="star_score">와  \<div class="score_reple">안에 위치했었다.
다음 코드를 이용해보면.

	score = lis[0].select_one('div.star_score > em')
    reple = lis[0].select_one('div.score_reple > p')
    
select_one()의 구조도 보면 알겠지만 해당 div 안에 em이나 p 안의 부분을 불러오는 명령이다.
이 상태로 출력을 하게 되면 다음과 같다.

>\<em>10\</em>
>\<p>\<span class="ico_best">BEST\</span>\<span class="ico_viewer">관람객\</span>이정재가 홍보 따라다닌 이유를 알겠음 ㅋㅋㅋ 그리고 관심병사 연기 도랐다  \</p>

코드가 거의 다 완성되어간다.
하지만 아직 태그가 제거되지 않아 이렇게 나타나게 된다.
이럴땐 get_text()함수를 이용하여 텍스트만 뽑아줄 수 있다.

적용해보면 다음과 같다.

    print('평점 :', score.get_text())
    print('리뷰 :', reple.get_text())
>평점 : 10 
>리뷰 : BEST관람객이정재가 홍보 따라다닌 이유를 알겠음 ㅋㅋㅋ 그리고 관심병사 연기 도랐다

이런식으로 가능하다.

이를 좀더 응용하면

	url = 'https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code='85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page=1
	
	response = req.urlopen(url)
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

----------

## 2.2 모든 페이지의 평점 크롤링 

좀더 응용해서 모든 페이지의 댓글을 크롤링해보자.

우선 마지막 페이지 번호를 가져와보자.
**총 댓글 개수를 가져와 10으로 나누어주고 소숫점은 올려주는 방식으로 페이지의 개수를 계산하려고 한다.**
자신이 필요한 평점의 갯수가 1000개 정도로 정해져 있거나 특정 영화만 분석할 경우에는 굳이 이런 코드가 필요가 없겠지만, 
여러 영화의 평점을 동시에 가져오는 경우라면 이런 코드가 휠씬 편리할 것이다.

마찬가지로 첫 페이지의 소스를 가져온다.

	url = 'https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code=85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page=1'
	
    response = req.urlopen(url)
    soup = BeautifulSoup(response, 'html.parser')

이제 댓글 전체 개수를 가져와야 하는데 아래 소스와 같이 \<strong class="total"> 안에서 두번째 \<em>에 위치하게 된다.

	...
	<div class="score_total">
		<strong class="total"><span class="tit"><em class="blind">140자 평</em></span><span class="sp">|</span>총<em>55,536</em>건</strong>

		<div class="best_score_info _bestPointHelp">
	....

따라서 같은 방식으로 전체 개수를 가져오게 되면

    score_total = soup.find('div', class_ = 'score_total')
    limit = score_total.find_all('em')[1]
    print(limit.get_text())
   >55,536
 
위와 같이 출력이 된다.
 
여기서 주의할 점은 출력된 숫자는 숫자형이 아니라 문자형이기 때문에 나눗셈과 같은 사칙연산이 불가능하다. 따라서 다음과 같은 과정을 통해 좀 더 가공이 필요하다.

	import math
	
    page_limit = int(limit.get_text().replace(',', ''))
    page_limit = math.ceil(page_limit/10)
    print(page_limit)
   >5554

math 라이브러리는 소숫점 올림 연산을 하기위해 불러왔다.

replace()함수를 이용해 콤마를 제거해준다. 이 과정이 없으면 숫자로 변형이 안된다.
그리고 숫자형으로 불러온뒤 10으로 나누어 준다. ceil()함수는 math 라이브러리 안에 있는 소숫점 올림 연산을 해주는 함수이다.

이제 마지막 페이지도 알아냈으니 해당 범위 안에서 평점들을 모두 뽑아내기만 하면 된다.
그러기 위해서는 url을 두개로 나누어준다.

	url1 = 'https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code=85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page='
	num = i
	
	url = url1+str(num)

위와 같은 방식으로 나누어준 뒤 num 변수를 1부터 위에서 계산한 마지막까지 돌려주기만 하면 된다.

	url1 = 'https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code='85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page='
	
    for num in range(1, page_limit+1):
        response = req.urlopen(url1+str(num))
        soup = BeautifulSoup(response, 'html.parser')
        score_result = soup.find('div', class_ = 'score_result')
        lis = score_result.find_all('li')
        
        for li in lis:
            score = li.select_one('div.star_score > em')
            reple = li.select_one('div.score_reple > p')
            print(score.get_text(), ' , ', reple.get_text(), '\n')
            
>10  ,  BEST관람객이정재가 홍보 따라다닌 이유를 알겠음 ㅋㅋㅋ 그리고 관심병사 연기 도랐다   
>10  ,  BEST관람객나 죽어서 지옥가면 어떡하냐 ㄷㄷㄷ 앞으로 착하게 살아야지   
>10  ,  BEST관람객남자 셋이서 안 울라고…주먹으로 입틀막함 ㅋㅋㅋㅋ   
>10  ,  BEST관람객여보, 이번 겨울에… 부모님댁에 밥솥 놔드려야겠어요   
>10  ,  BEST관람객가족이랑 봤는데… 나… 우리 아빠 우는 거 처음 봤음 ㅜㅜㅜㅜ   
>8  ,  관람객오늘도 열심히 사는 대한민국의 자홍,수홍씨들을 응원합니다 ㅠㅠ   
>10  ,  야 이거 생각한 것 보다 잘 나왔잖아 ?   
>10  ,  관람객오늘 화장 안하고 신과함께를 본 것은 신의한수였음.
>
> ...

나중에 데이터 처리하기 편하도록 평점과 리뷰사이는 콤마로 나누어 출력했다.

----------
## 2.3 텍스트 파일로 내보내기

이제는 가져온 자료들을 텍스트 파일로 내보낼 차례이다.

	f = open('Movie_Comment.txt', 'w')

open() 함수는 텍스트 파일을 불러오는 함수다.
뒤에 붙은 w는 명령어인데 텅 빈 턱스트 파일을 불러오고 텍스트를 입력하게 해준다.
단순히 입력된 데이터를 가져오는 경우라면 w대신 r을 넣어주면 된다.
**종합하자면, Movie_Comment.txt 라는 텍스트 파일을 생성하고 f에 불러와서 내용을 입력하게 해주는 내용이다.**

그 다음 진행할 내용은 전 단계와 비슷하다.

	url = 'https://movie.naver.com/movie/bi/mi/pointWriteFormList.nhn?code='85579&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page=1
	
	response = req.urlopen(url)
	print(response)soup = BeautifulSoup(response, 'html.parser')
	score_result = soup.find('div', class_ = 'score_result')
	lis = score_result.find_all('li')
	

	for li in lis:
	    score = li.select_one('div.star_score > em')
        reple = li.select_one('div.score_reple > p')
        f.write(score.get_text()+' , '+reple.get_text()+'\n')
    f.close 
**달라진 부분은 print() 부분이 그대로 f,write()로 바뀌어졌다는것 뿐이다.** 그러면 원래 출력될 부분이 그대로 텍스트 파일로 들어가게 된다.
마지막에 f.close로 입력을 끝내준다.
실행이 다 끝나면 텍스트 파일을 열어서 빠짐 없이 내보내졌는지 꼼꼼히 확인해보자!