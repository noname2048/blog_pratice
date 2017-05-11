## 블로그 만들기
***
안녕하세요, 중앙대학교 멋쟁이 사자처럼 5기 여러분!!

짧았던 시간을 거쳐서,
**게시판** 하나를 만들 수 있게 되었고, 
Post와 Comment가 들어간 **CRUD**를 스스로 할 수 있게 되었습니다

이제는 **모델**과 **컨트롤러**, 그리고 **뷰**를 자유자재로 수정할 수 있으며, 
저번시간에는 **gem**도 배워서 확장기능까지 추가해보았습니다

아직은 **ruby문법**이나 **rails문법**이 덜 익혀져서 조금 찾아보는 시간이 있을 수 있지만,
기본적인 웹 서비스를 만들 수 있는 토대 하나는 익히셨다고 생각합니다
만약 아직까지 CRUD에 대한 숙지가 되지 않으셨다면, ++꼭 두어번 만드시면서++ 정리하신다면 배우는데 있어서 좋은 결과에 도달하지 않을까 싶습니다

#### 오늘은 CRUD와 gem을 이용하여, 간단한 자신의 ==블로그==를 가져볼 계획입니다
몇가지 놓칠 수도 있는 부분을 최대한 자세히 설명하려고 노력하겠습니다. 꼭꼭 기초적인 부분을 짚고 넘어가시길 바랍니다!
이번 기회를 통해 어느정도 rails에 대해 윤곽이 잡히면 좋겠다는 각오를 다지면서, 
부족한 부분은 언제든 ++질문을 통해서++ 알아가셨으면 좋겠습니다

특히, **컨트롤러와 모델**은 ==ruby문법==에 대해서 숙지 하시는게 정말 많이 도움이 됩니다

>대표적인 예:
>```ruby
#do와 end는 한쌍이구요, 
#if-else-end 혹은 if-end 가 한쌍입니다 ㅠㅠ
#def-end도 한쌍이에요 ㅠㅠ
```
~~괄호 두개 열고 한개 닫는거 이상하잖아요ㅠㅠ 꼭 맞춰서 닫아 줍시다 (((()~~ 

**view** 부분은 ==html==과 ==css==를 잘 쓰실줄 아시면 이쁜 view가 나오며, 
더불어 ==헬퍼메서드==를 익혀야 노가다를 줄이게 됩니다
gem 이나 기타 꿀팁들은 하시면서 배우게 될것 같습니다


오늘 블로그를 위해 동원되는 젬은 총 ++4개++ 입니다
이미 익혔던 2개의 젬 외에, 추가로 ++3개++가 동원 됩니다
***
[devise](https://github.com/plataformatec/devise) : 유저에게 로그인을 할 수 있도록 해주는 젬
[paperclip](https://github.com/thoughtbot/paperclip) : 이미지 업로드를 도와주는 젬
[++rolify++](https://github.com/RolifyCommunity/rolify) : 로그인 한 유저에게 레벨(권한등급)을 부여하는 젬
[++will_pageinate++](https://github.com/mislav/will_paginate) : 블로그 글이 많을때 1.2.3…5 등으로 페이지 부여
[++faker++](https://github.com/stympy/faker) : 더미 데이터를 만들때 재미있는 랜덤 데이터를 생성하는 젬

***
더불어서 **view page**는 다음과 같이 구성될 예정입니다

>Welcome home
Posts index show new create edit update destroy


사용하는 모델은
>User
Post
Role

정도가 있겠네요

아마 제일 먼저 블로그 홈에 접속해 근황을 보고, 
posts 에서 게시글을 확인 할 수 있도록 하였습니다
**일반유저**는 자기글만 지울 수 있지만, **주인은** 모든게 가능하죠.

그리고 db:seed를 통해서  db:drop을 할때마다 아이디를 새로 만드는 일없이, 편하게 블로그를 테스트 할 수 있도록 최종적으로 할 계획입니다

***
여러분의 노가다 및 헷갈림을 매우 유발하는 디자인은 일부러 모두 뒤로 빼려고 노력했습니다. ~~한꺼번에 합시다. 한꺼번에.~~
***
자. 그럼 시작해 봅시다.

```ruby
gem 'devise' # 회원
gem 'rolify' # 롤 설정
gem 'will_paginate' # 페이지 넘기기
gem 'faker' # 더미데이터 생성
```

```bash
bundle install
```
> (rails 5.0.0 부터는 bundle install 대신 bundle만 쳐도 됩니다)
(rails 버전 확인하는 방법은 터미널에 ```rails -v```)
bash shell을 써서 ++bash창++ 이라 불리는데 (정확하게는 ++터미널++입니다)

그냥 plugin을 설치하는 과정이기 때문에 순서는 대략적으로 상관이 없습니다.
* [gem](http://ruby-korea.github.io/rubygems-guides/what-is-a-gem/) 이란 뭐였죠?

> gem 을 추가하고 나서, 항상 **CTRL-S**… (안하면 몸이 고달픔니다) 
> (설치가 겁나 빠르게 되면 이상하다고 느껴야 해요ㅠㅠ)
> gem 을 설치한 이후에는 언제나 **++서버 재시작++**을 잊지 맙시다!(stop, run)
> gem이 제대로 작동하기 위해서는 config/initialization 폴더를 서버가 시작하면서 읽어들여야 합니다.
 

자, 젬도 설치 했겠다, 이제 블로그의 view들을 만들어 봅시다

```bash
rails g controller Welcome home
rails g controller Posts index show new create edit update destroy
```

일반적으로 모델을 다루는 컨트롤러는 모델명 뒤에 **++s++**가 붙습니다
왜냐구요? ++resources++를 쓰기 위한 ==약속==[(컨트롤러 명명규칙)](http://guides.rorlab.org/action_controller_overview.html)입니다
resources는 ++RESTful++을 이용하는 잘 정리된 길입니다
잘 정리된 길이라 개발하는 모든 사람이 일반적으로 생각하는 라우팅 규칙이죠
RESTful은 get post put/patch delete 를 이용한 접근 방식입니다

모델은요?
```bash
rails g model Post title content:text user:references

```
>다음과 같이 쓸 수도 있습니다.
```bash
rails g model Post title content:text user:belongs_to
```
> 그리고 일반적으로, 이름 옆에 data_type을 붙여주지 않으면, default 값으로 string이 들어갑니다

마이그레이션도 잊지 마시구요!
```bash
rake db:migrate
```

자, 그럼 우리 했던 방식으로 갈까요?
몇번 해봤으니 금방 보이실 거라고 생각해요!
### post를 작성하는 일반게시판을 만드시오(10점, 20분)

(단, form_for와 link_to와 같은 뷰헬퍼 메서드를 이용해서 만드시오) 
***
**route.rb**랑 몇 가지 힌트를 드릴게요
```ruby
resources :posts
root 'welcome#home'
```
* root가 가장 밑에 오는 이유는 뭘까요?
* ==헬퍼메서드==는 뭔가요?

**form_for**는 다음과 같이 씁니다 우리가 자주쓰던 **form**에 비해 코드량이 확 줄었죠. (ㅎㅎ)
```ruby
<%= form_for(@post) do |f| %>
    <%= f.label '내용' %>
    <%= f.text_field :모델에있는거 %>
<% end %>
```
|f.?|기능|
|-|-|
|f.label|라벨입니다 뭔가 표시해주죠|
|f.text_field|일반적인 string을 담습니다|
|f.text_area|text를 담습니다|
|f.submit|버튼이 나와요|
|f.file_field|파일을 저장합니다|
|f.hidden_field|보이지 않는 양식입니다|
|...|그 외에도 엄청 많습니다|

**link_to**는 다음과 같이 씁니다

```ruby
<% link _to '목록으로', posts_path %>
```
근데 늘 헷갈리는게 있습니다
==_path==가 대체 뭐야…?

이건 **rake routes** 혹은 **/rails/info/routes**에서 확인할 수 있는데요 
테이블 앞에 **prefix**라고 되어있는거 보이시나요?
있는것도 있고, 없는것도 있긴 합니다만... 아마 메소드가 다른거지
대부분 있을 겁니다

**prefix**의 원래 의미는 **접두사** 입니다
왜 쓰냐면, 지금이야 괜찮지만, 나중에 링크가 무지무지 많거나, 
모델이 무지무지 많이 관계가 생기면, 주소가 굉장히 길어집니다 \=\=;;
==그러면 매우 귀찮습니다==
그래서 접두사를 이용하여, **url** 주소를 
대신 표한하는 **편리함**을 고안한 것이죠

이 접두사 뒤에 **_path**가 붙으면 상대주소고, **_url**이 붙으면 절대주소입니다.

- 절대주소? 상대주소가 뭐죠?

예를 들어볼까요,
**posts_path**는 **/posts** 로 대체되구요,
**posts_url**은 **http://너의홈페이지이름.c9.users.io/posts** 로 알아서
rails가 대체를 해버립니다 ~~어때요 참쉽죠?~~
> 실제로는 method(함수)의 리턴값을 이용한 것입니다.
> 참고로 ```:posts```를 해도 ```/posts``` 로 대체됩니다 (라우트 한정입니다)
> 이건 루비 문법에 심볼이라는걸 이용한건데
> 문자열 앞에 공백없이 ```:```이 붙으면 특수한 문자열이라는 뜻입니다.
> 대표적으로 ```:back```도 같은 예로 볼 수 있겠네요 

id가 필요한 show, edit 같은 경우에는 모델 자체가 주소가 되버립니다
```ruby
#show의 경우에는
<%= link_to '글보기', @post %>
#edit의 경우에는
<%= link_to '수정하기', edit_post_path(@post) %>
#delete의 경우에는
<%= link_to '삭제하기', @post, method: :delete %>

#만약 each do 구문을 썼다면!
<% @posts.each do |p| %>
	<%= link_to '링크', p, method: :delete %>
<% end %>

# link_to에 한가지가 아니라 여러가지를 담고 싶다면
<%= link_to @post do %>
	<img src=... >
    <span></span>
<% end %>
```
객체를 넣으면 알아서 설정이 된다는게 신기합니다
link_to의 역할은 a 태그를 좀더 쉽게 바꿔 쓰고자 하는건데
method를 바꾸려면 해쉬로 옵션을 추가해주기만 하면 됩니다

***

## 잠깐 멈춤!
==아래에는 답이 있습니다==

ruby문법을 잘이용하고, rails 규약을 이해하면 좋은 코드를 작성할 수 있습니다.
그러나 노가다로 풀었더라도, 보지 않고 풀었다면 잘 따라오신것 같네요


먼저 ```모델```들의 속한 관계 1:N을 설정해 줘야 되겠죠!
```ruby
class Post < ActiveRecord::Base
  #belongs_to :user
  #그런데 아직 user를 추가를 안해서...
end

```

다음은 Post와 Comment ```컨트롤러``` 입니다.

```ruby
class PostsController < ApplicationController
  before_action :find_post, only: [:show, :edit, :update, :destroy]
  
  def index
  	@posts = Post.all
  end

  def show
  end
  
  def new
    @post = Post.new
  end

  def create
    @post = Post.new(post_params)
    if @post.save
      redirect_to @post #post_path(@post)
    else
      redirect_to :back
    end
  end
  
  def edit
  end

  def update
    if @post.update(post_params)
      redirect_to @post
    else
      redirect_to :back
    end
  end

  def destroy
    if @post.destroy
      redirect_to :posts #posts_path랑 같아요
    else
      redirect_to :back
    end
  end
  
  private
  
  def find_post
    @post = Post.find(params[:id])  
  end
  
  def post_params
    params.require(:post).permit(:title, :content)
  end
  
end
```
> befor_action을 이용하여 id를 이용해 post를 찾는 걸 4줄을 1줄로 줄여 버렸습니다!
> post_params는 루비의 함수특성을 이용하고, 또 중요한 성질이 있죠.

- strong\_parameter란 무엇인가요?

<br/>

마지막으로 ```view``` 설정이 있겠네요.

```erb
<!-- index.html -->
<h2>Posts#index</h2>
<hr>
<%= link_to '새글', new_post_path %>
<table>
    <thead>
        <th>작성자</th>
        <th>시간</th>
        <th>제목</th>
        <th>액션</th>
    </thead>
    <tbody>
        <% @posts.each do |p| %>
        <tr>
            <td></td>
            <td></td>
            <td><%= p.title %></td>
            <td>
                <%= link_to '보기', p %>
                <%= link_to '수정', edit_post_path(p) %>
                <%= link_to '삭제', p, method: :delete, data: { confirm: 'Are you sure?' } %>
            </td>
        </tr>
        <% end %>
    </tbody>
</table>

<!-- show.html -->
<h2>Posts#show</h2>
<hr>
<p><%= @post.title %></p>
<p><%= @post.content %></p>
<p><%= link_to '돌아가기', :posts %></p>
<p><%= link_to '수정하기', edit_post_path(@post) %></p>


<!-- new.html -->
<h2>posts#new</h2>
<hr>
<%= form_for(@post) do |f| %>
  <div>
    <%= f.label :title %>
    <%= f.text_field :title %>
  </div>
  
  <div>
    <%= f.label :content %>
    <%= f.text_area :content, class: "form-control" %>
  </div>

  <div>
    <%= f.submit '제출하기' %>
  </div>
<% end %>
 
<!-- edit.html -->
<h2>posts#edit</h2>
<hr>
<%= form_for(@post) do |f| %>
  <div>
    <%= f.label :title %>
    <%= f.text_field :title %>
  </div>
  
  <div>
    <%= f.label :content %>
    <%= f.text_area :content, class: "form-control" %>
  </div>

  <div>
    <%= f.submit '제출하기' %>
  </div>
<% end %>
```

`rake db:migrate` 
***
## Chap2. rake db:seed 와 faker 이해
게시판을 만들긴 했지만, ```rake db:migrate``` 이후에는 게시글이나 전체적인 구도를 보기 위해서는 글을 여러번 다시 써야 한다는게 굉장히 불편합니다.

이를 위해서, rails는 자체적으로 미리 모델에 더미 데이터를 넣을 수 있는 기능을 제공하고 있습니다.

먼저, ```db>seeds.rb```에서
```ruby
for i in 1..5
	post = Post.new(title: i, content: i)
    post.save
end

5.times do
	Post.create(title: "a", content: "a")
end
```
라고 작성한뒤,
```bash
rake db:drop
rake db:migrate
rake db:seed
```
를 순서대로 입력하여 봅시다
~~오오~~ ~~노가다 해방~~

> 만약 추가되는 migration 테이블이 없다면 (있다면 migrate 명령이 필요합니다)
>```bash
rake db:reset
```
으로 3개의 명령어를 한방에 해결할 수 도 있고,
```bash
rake db:drop
rake db:setup
```
으로 순차적으로 나눠서 실행할 수 도 있습니다.

* for문에서 1..5 와 1...5의 차이는 뭔가요?

그러나 실제 더미데이터로 하기에는, 진짜 글인것 같진 않네요
너무 대강만든것 같습니다
뭔가 데이터를 진짜 같이 넣어주고 싶은데\.\.\. 
그래서 고안된 faker (가짜 데이터)를 한번 써보도록 합시다

우리가 사용할건, 의미 없는 글자로 이루어진 [Lorem](https://github.com/stympy/faker/blob/master/doc/lorem.md)과, 이름을 가져올 [Name](https://github.com/stympy/faker/blob/master/doc/name.md)을 사용합니다
==객체 개념을 사용하므로, 쉽게 이해되진 않는 코드이지만, 한번 따라해 봅시다==
```ruby
100.times do
	post = Post.new(title: Faker::Name.first_name, content: Faker::Lorem.paragraph)
    post.save
end
```
```bash
rake db:reset
```
와우\.\.\. 이제 게시글을 마음대로 테스트 해서 확인할 수 있습니다.
> 랜덤한 사진도 구현이 되어있기 때문에, 프로필에도 무리 없이 더미 데이타를 넣을 수 있습니다. 자세한건 github을 확인하세요

게시글을 많이 넣을 수 있게 되었습니다
근데 그러면 스압(스크롤 압박)이 생겨 버립니다

보통 많은 게시글을 한 페이지에 보여주는 것은 바람직하지 않습니다
그러므로, 우리는 웹툰이나 네이버 카페처럼, 일부만 가지고 와서 
페이지를 넘기며 보여줄 수 있도록 해 봅시다

## Chap3. will_pagenate 사용하기
index 에서 post를 모두 가져오는 대신에
```ruby
def index
  @posts = Post.all
end
```
와 같았던 코드를
아래와 같이 변경합니다
```ruby
def index
  @posts = Post.paginate(:page => params[:page], :per_page => 5)
end
```
```erb
<!--index.html -->
<h2>Posts#index</h2>
<hr>
<%= link_to '새글', new_post_path %>
<table>
    <thead>
        <th>작성자</th>
        <th>시간</th>
        <th>제목</th>
        <th>액션</th>
    </thead>
    <tbody>
        <% @posts.each do |p| %>
        <tr>
            <td></td>
            <td></td>
            <td><%= p.title %></td>
            <td>
                <%= link_to '보기', p %>
                <%= link_to '수정', edit_post_path(p) %>
                <%= link_to '삭제', p, method: :delete, data: { confirm: 'Are you sure?' } %>
            </td>
        </tr>
        <% end %>
    </tbody>
</table>
<%= will_paginate @posts %>
```
> 맨 아래줄에 `<%= will_paginate @posts %>`가 삽입되었습니다

꽤나 간단하게 젬을 추가하여 사용하여 보았습니다.

## Chap4. devise + rolify
먼저 앞서서 배웠던 devise입니다 설치를 진행하도록 하겠습니다
콜론이 띄어쓰기가 없다는 점에 주의하시길 바라며, 
실수할 경우에는 g(generate)대신 d(destroy)를 이용하여, 오타 그대로 지워주세요
> 보통 터미널에서 방금전 실행한 명령어를 불러오는 방법은 화살표로 윗방향키를 누르면 됩니다. 계속 누르면, 2번째, 3번째 전에 실행한 명령어롤 실행할 수 있도록 창에 자동으로 입력하여 줍니다. 엔터를 누르기 전까지 실행이 되지 않으니 방향키를 위 아래로 눌러보시기 바랍니다.

실행명령어는 각각
설치, 모델 생성, 뷰생성, db 업데이트 순입니다
```bash
rails g devise:install
rails g devise User
rails g devise:views
rake db:drop
rake db:migrate
```
>   devise_for :users가 설치로 인해 route에 자동으로 추가됩니다
>  > 사실 devise를 굉장히 간단하게 사용하고 있지만,
> 내포하는 기능을 사용하면 확인용 이메일을 발송하도록 설정할 수도 있습니다.
> 자세한건 devise github과, 초레가2 devise 젬 파헤치기(v1.0.3) 버전을 확인해 주세요

모델이 새로 생기게 되었습니다
1:N 관계를 잘 살펴보시기 바랍니다
(누가 누구에 속할 수 있는지)
```ruby
class Post < ActiveRecord::Base
  belongs_to :user
end

class User < ActiveRecord::Base
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable
         
  has_many :posts
end
```
그리고, **rolify**로 권한을 설정하도록 하겠습니다.
터미널에 다음을 입력합니다

```bash
rails g rolify Role 
```
그러면 `role.rb`파일이 생성됩니다.
여기에서
```ruby
class Role < ActiveRecord::Base
  has_and_belongs_to_many :users, :join_table => :users_roles

  belongs_to :resource,
             :polymorphic => true,
             :optional => true

  validates :resource_type,
            :inclusion => { :in => Rolify.resource_types },
            :allow_nil => true

  scopify
end
```
`optional` 부분을 빼주어야 합니다.
아래와 같이 고쳐주세요
```ruby
class Role < ActiveRecord::Base
  has_and_belongs_to_many :users, :join_table => :users_roles

  belongs_to :resource,
             :polymorphic => true

  validates :resource_type,
            :inclusion => { :in => Rolify.resource_types },
            :allow_nil => true

  scopify
end
```
그리고 `user.rb`에서 다음과 같이 수정합니다
설명은 튜터님께 맡기도록 하겠습니다
```ruby
class User < ActiveRecord::Base
  #새로운 유저, 2명째 부터는 권한을 default로 설정합니다
  after_create :set_default_role, if: Proc.new { User.count > 1 }
  
  #rolify기능을 사용합니다 
  rolify
  
  #devise로 인해 사용할 수 있는 함수들입니다  
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable
  
  #모델에서의 관계입니다
  has_many :posts
  
  #기본 권한을 지정하는 함수입니다
  def set_default_role
    add_role :user
  end
end
```
대강 다 되었습니다!
아, 가기전에 잠깐 `role의 migration table`을 살펴봅시다
권한에 `:user`와 `:admin`이 있는것 보이시나요?
```ruby
class RolifyCreateRoles < ActiveRecord::Migration
  def change
    create_table(:roles) do |t|
      t.string :name
      t.references :resource, :polymorphic => true

      t.timestamps
    end
    
	#여기에 권한이 지정되어 있습니다
    create_table(:users_roles, :id => false) do |t|
      t.references :user
      t.references :role
    end

    add_index(:roles, :name)
    add_index(:roles, [ :name, :resource_type, :resource_id ])
    add_index(:users_roles, [ :user_id, :role_id ])
  end
end
```
좋습니다. 지금까지 모델을 통해 세팅을 하였고, 
제대로 작동하는지 한번 검증을 해보도록 하겠습니다
***
### 글만 확인해보기
`seed.rb` 파일을 다음과 같이 수정합니다
```ruby
admin = User.create(:email => 'admin@gmail.com', :password => 'master')
admin.add_role :admin
admin.save

user = User.create(email: 'user@gmail.com', password: 'hi')
#아마 after_create 에서 자동으로 user권한을 추가 시킬 겁니다!
user.save

5.times do
    Post.create(
        title: Faker::Name.first_name, 
        content: Faker::Lorem.paragraph,
        user: admin)
    Post.create(
        title: Faker::Name.first_name, 
        content: Faker::Lorem.paragraph,
        user: admin)
end
```
젬설정이 완벽하게 되지 않아서인지 `rake db:reset`이 먹히질 않는군요 
> 사실 `rake db:reset`은 스키마를 불러오는 다른 기능도 숨어있는데, 이게 오류를 불러일으키나 봅니다

따라서 다음을 실행합니다
```bash
rake db:drop
rake db:migrate
rake db:seed
```
> 이 다음부터 `rake db:reset`이 가능할 겁니다.

그리고 /posts 로 가면 뭔가 조금 이상해 보입니다
작성자랑 시간이 아직 추가되지 않았군요
`index.html`과 `show.html`에 다음을 추가해 봅니다
```erb
<%= p.user.email %>
<%= p.created_at %>
```
네. 모든게 잘 동작하는 군요!
이제 컨트롤러에서 각각 설정을 완료하면 될것 같습니다!
***
### 권한에 따른 동작 부여 및 블로킹하기
먼저 `PostController`에서
```ruby
before_action :authenticate_user!, except[:index]
```
를 추가합니다 글을 보려면 이제 로그인이 되어있어야 합니다

로그인 정보를 확인하기 위해 다음과 같이 추가합니다
`application.html`에서
```erb
<body>
<%= link_to 'HOME', '/' %>
<%= link_to 'POST', '/posts'%>
<hr>
  <% if user_signed_in? %>
    <p>현재 로그인된 유저의 이메일 : <%= current_user.email %></p>
    <p><%= link_to "My Profile", edit_user_registration_path %></p>
    <p><%= link_to "로그아웃", destroy_user_session_path, method: :delete, data: { confirm: "Are you sure?" } %></p>
  <% else %>
    <p><%= link_to "Sign in", new_user_session_path %></p>
    <p><%= link_to "Sign up", new_user_registration_path %></p>
  <% end %>
<hr>

<%= yield %>

</body>
```
> 추가적으로 nav 역학을 할 상단의 링크도 미리 넣어보았습니다

이제 공통적으로 로그인을 해야 index외의 기능이 가능합니다
이제 자기글만 지울 수 있도록 권한을 부여하되,
admin은 자기 외에 글도 지울수 있어야 하겠죠.

생각해보면, 
show, new, create, edit, update, destroy는 이미 로그인이 필 수 입니다
destroy만 admin에게 모두 열어주고
edit, update는 작성자에게만 열어주면 될것 같습니다

- edit과 update모두 컨트롤러에서 막는 이유는 뭔가요?
- private가 뭐죠?

`PostController` 에서
다음 두 함수를 추가해 보았습니다
```ruby
def admin_or_user
    unless current_user.has_role?(:admin) or @post.user == current_user
      redirect_to :back
    end
end
  
def only_user
    unless @post.user == current_user
      redirect_to :back
    end
end
```
두 함수는 모두 주어진 조건을 만족하지 않으면 링크를 무효화합니다
이를 이용하면 다음과 같이 미리 블로킹을 하는게 가능합니다.
`before_action` ==두 줄==이 추가된것을 유심히 보시기 바랍니다
`PostController`
```ruby
class PostsController < ApplicationController
  before_action :authenticate_user!, except: [:index]
  before_action :find_post, only: [:show, :edit, :update, :destroy]
  before_action :admin_or_user, only: [:destroy]
  before_action :only_user, only: [:edit, :update]

  def index
    @posts = Post.paginate(:page => params[:page], :per_page => 5)
  end

  def show
  end

  def new
    @post = Post.new
  end

  def create
    @post = Post.new(post_params)
    @post = current_user
    if @post.save
      redirect_to @post #post_path(@post)
    else
      redirect_to :back
    end
  end

  def edit
  end

  def update
    if @post.update(post_params)
      redirect_to @post
    else
      redirect_to :back
    end
  end

  def destroy
    if @post.destroy
      redirect_to :posts #posts_path랑 같아요
    else
      redirect_to :back
    end
  end

  private

  def find_post
    @post = Post.find(params[:id])  
  end

  def post_params
    params.require(:post).permit(:title, :content)
  end

  def admin_or_user
    unless current_user.has_role?(:admin) or @post.user == current_user
      redirect_to :back
    end
  end
  
  def only_user
    unless @post.user == current_user
      redirect_to :back
    end
  end
  
end
```
그런데 수정과 삭제 링크가, 되지 않는다면 아예 안보여주는게 낮겠죠?
어떻게 할까요?
```ruby
if current_user == p.user

```

### 자유롭게 수정하기
다음과 같은게 가능합니다
- wiil_pagenate에서 5개가 아니라 10개씩 불러와 보세요
- admin과 user계정의 이메일이나 비밀번호를 바꿔보세요
- nick_name을 추가할 수 있나요?
- devise의 view를 자유롭게 수정해보세요

그리고 위에 바꾸기 전에 ++clone++해서 다음 시간에 추가로 설치되는 사항들을 준비하시기 바랍니다 (또, 과제도 나가니 미리 되는 프로젝트를 복사본으로 만들어 두는 것도 좋겠죠!)
자신의 블로그를 마음껏 즐기시기 바랍니다
> 복습해볼까요
> - 다음젬의 역할을 설명해 보세요
> faker, will_pagenate, devise, rolify
> - devise를 설치하기 위해서 어떤 명령어들을 입력했나요?
> 
> - seed.rb는 뭐하는 파일이죠?
> 
> - before_action과 after_create의 사용법과 사용된 곳을 말해보세요
> 
> - for문과 times문을 자유롭게 쓸 수 있나요?
>
> - Post.new를 한줄로 적을 수 있나요?
>
> - Post.new와 Post.create의 차이점을 말해봅시다
> 
> - StrongParameters?
> 
> - unless 문은 if와 어떤 다른점이 있었나요?
> 
> - redirect_to문을 어떻게 사용하나요?
> 
> - prefix와 link_to, 를 잘 사용할 수 있나요?
> 
> - form_for와 form_tag, form 셋다 사용해 봤습니다(이번시간 말구요) 차이점이 뭔가요?
> 
> - resources와 RESTful을 설명해 봅시다
> 
> - 컨트롤러와 모델의 명명규칙?
> 
> - 문자열 앞에 `:`이 붙으면 어떤 뜻인가요?


**과제 : 담주 수요일까지 나만의 블로그 디자인 해오기**
컨트롤러에 수정이 1개이상, 모델수정이 1개이상, 뷰 수정이 2개이상 있을것
(부트스트랩은 과제용 프레임워크에서 제외하겠습니다)

그러면 마지막 단계로 넘어가 봅시다
***
### 디자인 하기
마크업이 모두 끝났습니다
이제 이 요소들을 얼마나 이쁘게 배치하느냐에 따라서 블로그방문객의 수가 결정 될 것입니다. 아래는 부트스트랩으로 한번 시도해 보도록 하겠습니다
자신이 원하는 HTML/CSS 프레임워크가 있다면, 그것을 사용해주세요

먼저 [부트스트랩4](https://v4-alpha.getbootstrap.com/getting-started/download/)에서 CDN을 가져옵니다
```erb
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-alpha.6/css/bootstrap.min.css" integrity="sha384-rwoIResjU2yc3z8GV/NPeZWAv56rSmLldC3R/AZzGRnGxQQKnKkoFVhFQhNUwEyJ" crossorigin="anonymous">
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-alpha.6/js/bootstrap.min.js" integrity="sha384-vBWWzlZJ8ea9aCX4pEW3rVHjgjt7zpkNpZk+02D9phzyeVkE+jo0ieGizqPLForn" crossorigin="anonymous"></script>
```

Ajax를 쓰기 위한 [jQuery](https://developers.google.com/speed/libraries/#jquery)도요
```erb
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script>
```
jquery가 bootstrap 보다 먼저있어야 한다는 점을 주의하시기 바랍니다
- 왜그럴까요?

부트스트랩4에서 가져온 `nav`형식을 `application.html`에 적용시키겠습니다
이뻐진 `nav`를 확인할 수 있습니다.
```erb
<body>
<header>
  <nav class="navbar navbar-toggleable-md navbar-light bg-faded">
  <button class="navbar-toggler navbar-toggler-right" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
  <a class="navbar-brand" href="/">블로그</a>
  <a class="navbar-brand" href="/posts">post</a>
  
  <div class="collapse navbar-collapse" id="navbarSupportedContent">
    <div class="d-flex flex-row-reverse" style="width:100%">
      <ul class="navbar-nav">
        <% if user_signed_in? %>
          <li class="nav-item">
          	<%= current_user.email %>
          </li>
          <li class="nav-item">
            <%= link_to '로그아웃', destroy_user_session_path, class: 'nav-link', method: 'delete' %>
          </li>
        <% else %>
          <li class="nav-item">
            <%= link_to '로그인', new_user_session_path, class: 'nav-link' %>
          </li>
          <li class="nav-item">
            <%= link_to '회원가입', new_user_registration_path, class: 'nav-link' %>
          </li>
        <% end %>
      </ul>
    </div>
  </div>
</nav>
</header>
<div class="container">

<%= yield %>

</div>
<footer>
  
</footer>
</body>
```

마찬가지로 `list`와 `form`을 이용하여 `view`단을 꾸밀 수 있습니다
`index.html`
```erb
<div class="list-group">
    <% @posts.each do |post| %>
      <%= link_to post, class: "list-group-item list-group-item-action flex-column align-items-start" do %>
        <div class="d-flex w-100 justify-content-between">
          <h5 class="mb-1"><%= post.title %></h5>
          <small><%= post.created_at %></small>
        </div>
        <p class="mb-1"><%= post.content %></p>
        <small><%= post.user.eamil %></small>
      <% end %>
    <% end %>
</div>
<%= will_paginate @posts %>
<div class="card-options">
    <% if user_signed_in? and current_user.has_role?(:admin) %>
        <%= link_to '새글쓰기', new_post_path, class: "btn btn-primary card-link" %>
    <% end %>
</div>
```
`show.html`
```erb
<div class="card">
  <div class="card-block">
    <h4 class="card-title"><%= @post.title %></h4>
    <h6 class="card-subtitle mb-2 text-muted"><%=  %></h6>
    <p class="card-text"><%= @post.content %></p>
    <div class="card-options">
        <% if user_signed_in? and ( current_user == @post.user or current_user.has_role?(:admin) ) %>
            <%= link_to '편집', edit_post_path(@post), class: "card-link" %>
            <%= link_to '삭제', post_path(@post), class: "card-link", method: 'delete' %>
        <% end %>
    </div>
  </div>
</div>
```
`new.html`
```erb
<%= form_for(@post) do |f| %>
  <div class="field">
    <%= f.label :title %>
    <%= f.text_field :title, class: "form-control" %>
  </div>

  <div class="field">
    <%= f.label :content %>
    <%= f.text_area :content, class: "form-control" %>
  </div>
  
  <div class="actions">
    <button type="submit" class="btn btn-primary">작성하기</button>
  </div>
<% end %>
```
`edit.html`
```erb
<%= form_for(@post) do |f| %>
  <div class="field">
    <%= f.label :title %>
    <%= f.text_field :title, class: "form-control" %>
  </div>

  <div class="field">
    <%= f.label :content %>
    <%= f.text_area :content, class: "form-control" %>
  </div>

  <div class="actions">
    <button type="submit" class="btn btn-primary">작성하기</button>
  </div>
<% end %>
```
