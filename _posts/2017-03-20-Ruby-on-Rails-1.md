---
layout: post
title:  "Ruby on Rails - Board 만들기 #1"
date:   2017-03-20
desc: "Ruby on Rails - scaffold"
keywords: "ruby, ruby on rails, rails, scaffold"
categories: [Ruby]
tags: [ruby, ruby on rails, scaffold]
---

`Java` 와 `SpringFrameWork`를 사용한 Project 경험만 가지고 있던 중에 `Ruby on Rails` 를 이용한 개발을 해야 하는 환경이 생겼고, 그 첫 주제로 간단한 BBS(게시판)을 만들어야 한다. `Ruby` 와 `Ruby on Rails` 모두 처음 접하기 때문에 간단한 Board 를 만드는 작업에도 학습이 필요 하다고 생각된다. 그래서 학습하면서 만들어 가는 내용을 기록 할 겸 Posting 하려고 한다.

진행 방식은 단위 별로 작업을 나누고 각각의 이터레이션 에 해당하는 작업을 진행 하면서 학습하게 된 내용을 기록 하려고 한다.
상세 하게 모든 부분을 전부 기술 하기에는 시간이나 다루어야하는 범위에 대한 제약이 있기 때문에 기본적으로 `rails 기본 가이드` 와 `ruby syntex` 그리고 `gem, bundle, rake` 등의 정보는 참고 자료나 책을 통해 학습을 선행 했음을 가정 하겠다.

----

## **이터레이션 1 [ 기본 구조 준비 ]**
### 1-1. scaffold 를 이용한 기본 구조 생성.
**Ruby on Rails** 에는 `generate` 라는 command 를 이용하여 자동으로 필요한 file 을 생성 할 수 있다.
> generate 와 rails 에서 제공 하는 command Line 참고:[ruby on rails-commandLine](http://guides.rorlab.org/command_line.html)

`generate` 를 이용한 생성중 `scaffold`를 이용해 기본 구조를 생성 한다.

`scaffold`를 이용한 생성은 controller, model, view, route, test 와 관련된 모든 file 들을 자동으로 generate 해준다.

#### Command Line - generate scaffold
```bash
$>{project_home}/rails generate scaffold Post
```
* rails generate
  + generate 를 사용하기 위한 rails command.
* scaffold
  + 생성 할 작업.( ex. contoller, model, migration 등 다양한 generate 생성이 가능하다. )
* Post(name)
  + 생성 할 작업의 이름 (ex. Post 로 이름 지정시 posts_controller.rb, post.rb 등 지정한 이름을 사용하여 file이 생성된다.)

Command Line 실행 시 자동으로 만들어지는 file 목록의 Log 를 확인 할 수 있다.

`scaffold` 를 이용한 generate 시 *controller, model, view, route setting, test* 에 대한 모든것이 자동으로 준비 된다는게 중요한 사실이다.  위에서 사용한 command Line 은 기초적으로 생성하는 command 이며, 추가적으로 Model 에 옵션을 더 부여 하는 생성 도 가능 하다.

### 1-2. Model in Add Filed

기본 command 를 이용하여 `scaffold` 생성시 post Model은 그저 DataBase 에 `posts` 라는 테이블 이라는 정보 이 외 아무런 컬럼도 가지지 않는다. 그렇기 때문에 Database 에 migration 을 하기 전에 migration file을 수정 하여 posts table에 필요한 컬럼들을 정의 해 주어야 한다.
> ruby on rails 에서 Model은 `Active Recored` 패턴을 구현한 `ORM` 시스템이다.

migration 을 위한 file은 `scaffold`로 generate 시 `db/migrate/` 밑에 `{VERSION_NUMBER}_create_{NAME}.rb` 형태로 생성 된다.(ex. Post 로 생성 시 {VERSION_NUMVER}_create_posts.rb )

migration file 을 열고 간단히 아래와 같이 title, contents 컬럼을 추가 해 준다. `rails` 의 migration 은 id 와 careate/update time 컬럼을 자동으로 생성해 준다. 물론 원하는 컬럼으로 재정의 해서 사용 할 수도 있다.

*migration file - {VERSION_NUMBER}_create_posts.rb*
```ruby
class Posts < ActiveRecord::Migration
  def change
    create_table :posts do |t|
      t.string :title
      t.text :contents
      t.timestamps
    end
  end
end
```
> 변경 한 posts migration file의 모습




filed 정의와 함께 migration file의 준비가 끝났다면 아래의 command 를 이용하여 posts table 에 대한 정보를 Database에 migration 적용 한다.
**Command Line - migrate**
```bash
$>{project_home}/rake db:migrate
```
> Data Base 에 새로운 migration을 적용.

여기까지가 이터레이션1 로 정한 분할 범위 이며, 기본적인 구조 준비는 마친 샘이다.

글 등록,수정,삭제,목록 을 모두 사용 할 수 있는 back-end 와 view page 를 모두 준비 했다. `scaffold` 생성시 자동으로 생성된 **resource_route** 로 인해 즉시 view 와 기능에 대한 확인이 가능하다.

**Command Line - rails server start**
```bash
$>{project_home}/rails server
```
> rails server start command line

아래와 같은 URL 에 해당하는 View 와 기능을 확인해 볼 수 있다.
* base_path : `localhost:3000`
  + {base_path}/posts - index page
  + {base_path}/posts/new - new page
  + {base_path}/posts/{:id} - show page
  + {base_path}/posts/{:id}/edit - edit page

REST 방식 이기 때문에 `create, update, destroy` 에 대한 기능 확인은 각각에 해당하는 `post,put,delete` 호출을 통해 확인이 가능하다.

## **Referance**

----

* [Ruby on Rails 공식 가이드](http://guides.rubyonrails.org/index.html)
