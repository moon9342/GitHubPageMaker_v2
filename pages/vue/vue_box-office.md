---
title: Vue BoxOffice 실습예제
sidebar: vue_sidebar
summary: ""
permalink: vue_box-office.html
folder: vue
---

## 영화 BoxOffice 실습예제 구현

영화진흥위원회 Open API와 KAKAO 이미지 검색 API를 이용하여 간단한 BoxOffice 순위를 검색하는
웹앱을 구현해보도록 하겠습니다. 

우리예제는 `https://cors-anywhere.herokuapp.com/`를 이용한 proxy설정을 이용하기 때문에 같은 domain으로 여러번
request를 보내게 되면 block됩니다. 그래서 live server 세팅을 변경해서 사용하는 포트번호를 다르게 변경해주면 block되지 않고
사용할 수 있습니다. 

~~~javascript

// live server의 settings.json

{
    "liveServer.settings.ChromeDebuggingAttachment": false,
    "liveServer.settings.port": 12355,
}

~~~

~~~html
{% raw %}

<html>
<head>
    <title>Vue with Axios Sample</title>
</head>
<body>
<div id="app">
    <input type="date" v-model="searchDate">
    <button v-on:click="getData">영화 박스오피스 순위가져오기</button>
    <show-movie v-bind:moviedatas="movies"></show-movie>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script>

    class CreateMovieObj {
        constructor(rank, movieNm, openDt) {
            return (async () => {
                this.rank = rank
                this.posterUrl = await axios({
                    method: 'get',
                    url: 'https://cors-anywhere.herokuapp.com/dapi.kakao.com/v2/search/image',
                    headers: {'Authorization': 'KakaoAK c5f509547ebddb71b44b308f3ddf39ed'},
                    params: {
                        query: movieNm
                    },
                    responseType: 'json',
                }).then(function(response) {
                    return response.data.documents[0].thumbnail_url;
                }).catch(function(err) {
                    console.log(err);
                });
                this.movieNm = movieNm,
                        this.openDt = openDt

                return this;
            })();
        }
    }

    Vue.component('show-movie', {
        props: ['moviedatas'],
        template: `
        <table>
            <thead>
            <tr>
                <th scope="col">번호</th>
                <th scope="col">포스터</th>
                <th scope="col">영화제목</th>
                <th scope="col">오픈일</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="movie in moviedatas">
                <td>{{movie.rank}}</td>
                <td><img v-bind:src="movie.posterUrl"></td>
                <td>{{movie.movieNm}}</td>
                <td>{{movie.openDt}}</td>
            </tr>
            </tbody>        
        </table>
        `
    });

    new Vue({
        el: '#app',
        data: {
            searchDate: '',
            movies: []
        },
        watch: {
            searchDate: function(data) {
                console.log(data + '로 변경되었습니다.');
            },
            movies: function(data) {
                console.log(data);
            }

        },
        methods: {
            makeData: async function(arr) {
                this.movies.length=0;
                for(const movie of arr) {
                    // 생성자 함수 호출                    
                    this.movies.push(await new CreateMovieObj(movie.rank, movie.movieNm, movie.openDt));
                }
            },
            getData: async function() {
                this.makeData(
                        // await은 async 함수에서만 사용가능.
                        await axios({
                            method: 'get',
                            url: 'https://cors-anywhere.herokuapp.com/https://www.kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json',

                            // headers는 사용자 지정 헤더입니다.
                            // headers: {'X-Requested-With': 'XMLHttpRequest'},

                            params: {
                                key: 'f5eef3421c602c6cb7ea224104795888',
                                targetDt: this.searchDate.replace(/-/g, '')
                            },
                            data : {},
                            responseType: 'json',
                        }).then(function(response) {
                            console.log('성공!!');
                            return response.data.boxOfficeResult.dailyBoxOfficeList;
                        }).catch(function(err) {
                            console.log(err);
                        })
                );

            }
        }
    });
</script>
</body>
</html>


{% endraw %}
~~~

End.

{% include links.html %}
