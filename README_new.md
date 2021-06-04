### 작성자 : LG CNS Enterprise분석3팀 한규인 사원
### 최초작성일 : 2021. 05. 31.

# 개 요

 해당 프로젝트는 Bert 모델을 구현하는 것을 목표로 합니다.
 Bert-based를 Tensorflow v2, 한국어 기준으로 구현합니다.
 데이터는 Ko-Wiki 2021년 5월 20일 기준, 최신 위키 문서를 학습합니다.

# 구 축

### 학습 데이터 및 전처리

 데이터 파일명
 kowiki-20210520-pages-articles.xml : 3.5GB, 793 Files, 176610+356970 articles
 Linux Ubuntu 환경에서 wikiextractor library를 이용하여 데이터를 추출합니다.

####  Documents Filter & Sentences Transfer

 0. ';'가 포함된 경우 wiki tag가 남아있는 경우로 데이터 제거
    'colspan'의 경우 테이블 데이터가 남아있는 경우로 데이터 제거
 1. 영어 대문자는 모두 소문자로 치환 (Uncased)
 2. 괄호 및 괄호 안에 있는 내용 제거  <---- 21. 05. 31. PATCH 괄호 안도 의미를 가질 수 있다고 생각하여 남김
 3. N.S.Vierson 등 예외처리
 4. 소수점 토큰 예외처리
 5. 1. ~~~, 2. ~~~ 예외처리
 6. \xa0 (character decoding 문제), 공백으로 변환
 7. 연속된 공백 처리
 8. sp. --> speices의미로 예외처리
 9. '.' 으로 split
 !!! < -- Wiki 내용이다보니 문장이 !, ? 등으로 끝난 사례가 거의 존재하지 않음 -- > !!! 추후엔 확인해야할 것
 10. 문장 strip
 11. 공백문장 제거
 12. 문장은 최소 min_seq_len(3) 길이 이상이 되어야 함.
 13. 예외처리 (__)를 다시 (.)으로 변환

 위와 같은 과정에 의해 533,580개 article(documents) 중 359,497(135,099 + 224,398)개 documents를 선별하였음
 Total Sentences : 3,899,088개 문장


 ### Tokenize

from tokenizers import BertWordPieceTokenizer 객체를 이용하여 전체 말뭉치를 가지고 Tokenizer를 학습하였다.

Parameter는

-- 객체 파라미터
- vocab_file=None,                                    # ???
- clean_text=True,                                    #
- handle_chinese_chars=True,                          # 중국어를 뺄거면 해당 내용을 False로
- strip_accents=False, # Must be False if cased model # 한국어에서는 반드시 False
- lowercase=False,                                   
- wordpieces_prefix="##"

-- 훈련 파라미터
- limit_alphabet = 6000
- vocab_size = 32000


Q1. 한국어 문장 분리기 라이브러리가 존재했다 ! kss
Q2. 한자, 일본어, 특수문자 등을 제거하지 않았다. ~와 ⁓을 구분함.

  ### Pretrained

-Bert-Base

vocab_size = 32000
max_seq_len = 256
num_layers = 12
d_model = 768
dff = d_model * 4 = 3072
num_heads = 12
dropout = .1

 - q-small
vocab_size = 32000
max_seq_len = 130
num_layers = 6
d_model = 384
dff = d_model * 2 = 768
num_heads = 6
dropout = .1
