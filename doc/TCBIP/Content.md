# Transforming Code into Beautiful, Idiomatic Python

## Raymond Hettinger
Python 주요 개발진 중 한명 

아래 소개되는 방식들을 실제로 구현하고 Python에 적용시킨 인물

<details open>
<summary>Table of Contents</summary>

1. [Looping](#Looping)
2. [Dictionaries](#Dictionaries)
3. [Clarity](#가독성)
4. [Tuple Packing and Unpacking](#튜플)
5. [Decorators and Context Mangers](#데코레이터)
6. [Concise Expressive One-Liners](#원라이너)
7. [후기](#후기)
</details>

## Looping
 1. 전통적인 인덱스를 사용한 Looping을 하지말고 Python의 핵심 Looping 관용구를 사용하자
 2. for-else문과 두가지 iter() 함수의 매개변수 형태를 사용하여 고오급 기술을 배워보자
 3. 깔끔하고, 관용적이며, 심지어 더욱 빠른 Python 코드를 짜려 노력하자
 + 예시로 나오는 것 중 `기존방식`이란, Python 외 C같은 언어를 하고 온 사용자들이 주로 작성하는 것을 말한다.
 * Numbers
    - 기존 방식
    ```py
        for i in [0,1,2,3,4,5]:
            print i ** 2
    ```
    - range()를 활용한 방식(Python 2.7식 range함수)
    
    ```py
        for i in range(6):
            print i ** 2
    ```
    - xrange()를 활용한 방식(Python 3이상에서는 기존 range를 대체)
    ```py
        for i in xrange(6):
            print i ** 2
    ```

    + range(x)는 우선 x 사이즈 만큼의 리스트를 생성하기에 값이 큰 경우 메모리를 많이 먹음
    + xrange(x)는 looping 할 때마다 하나씩 생성되는 iterator를 재생시킴
 * Collection

    ```
        collection = ["Apple", "Banana", "Citrus", "Dimple"]
    ```
    - 기존 방식
    ```py
        for i in range(len(collection)):
            print collection[i]
    ```
    - 파이썬 방식
    ```py
        for fruit in collection:
            print fruit
    ```
 * 반대로 Looping 하는 법
    ```py
        colors = ["Red", "Blue", "Yellow", "Green"]
    ```
    - 기존 방식
    ```py
        for i in range(len(colors)-1, -1, -1)):
            print colors[i]
    ```
    - 파이썬 방식
    ```py
        for color in reversed(colors):
            print color
    ```
 * 인덱스와 함께 값까지 Looping 하는 법
    ```py
        colors = ["Red", "Blue", "Yellow", "Green"]
    ```
    - 기존 방식
    ```py
        for i in range(len(colors)):
            print i, "-->", colors[i]
    ```
    - 파이썬 방식
    ```py
        for i, color in enumerate(colors):
            print i, "-->", color
    ```
    - enumerate()은 iterator로, C로 구현되어 있어서 빠르기까지 하다

 * 두가지 Collection을 함께 Looping하는 법
    ```py
        names = ["Sean", "John", "Ben", "George"]
        colors = ["Red", "Blue", "Yellow", "Green"]
    ```
    - 기존 방식
    ```py
        n = min(len(names), len(colors))
        for i in range(n):
            print names[i], '-->', 'colors[i]'
    ```
    - zip() 활용한 방식
    ```py
        for name, color in zip(names, colors):
            print name, '-->', color
    ```
    - izip()
    ```py
        for name, color in izip(names, colors):
            print name, '-->', color
    ```
    + zip(x,y)은 기존 x, y 리스트 뿐만 아니라 세번째 리스트를 생성해서 기존 두개를 저장하고, 세번째 리스트 내에 있는 값은 x, y의 값들을 tuple로 저장하기 때문에 메모리를 많이 먹는다. 
    + izip()은 iterator 객체를 생성하므로 zip보다 메모리 측면에서 효율적이다


 * 정렬 후 Looping
    ```py
        for color in sorted(colors):
            print color
    ```
    ```py
        for color in sorted(colors, reverse=True):
            print color
    ```
 
 * 커스텀 정렬하는 법
    - 기존 방식
    ```py
        colors = ["Red", "Blue", "Yellow", "Green"]
        def compare_length(c1, c2):
            if len(c1) < len(c2): return -1
            if len(c1) > len(c2): return 1
            return 0
        print sorted(colors, cmp=compare_length)
    ```
    
    - 파이썬 방식
    ```py
        print sorted(colors, key=len)
    ```

    + 기존 방식은 만약 리스트 내에 값이 많다면, 해당 `compare_length`함수는 nlogn 만큼 호출되게 된다.
    + 파이썬 방식으로 하게되면 n 번 호출 되므로 효율적일 때가 더 많을 것이다.

 * 특정 값이 나올 때까지 함수 호출
    - 기존 방식
    ```py
        blocks = []
        while True:
            block = f.read(32)
            if block == '':
                break
            blocks.append(block)
    ```
    - Pythonic way
    ```py
        blocks = []
        for block in iter(partial(f.read, 32), ''):
            blocks.append(block)
    ```
    + `functools.partial`을 사용하고, iter(object, sentinel) 즉 특정 sentinel 값에 이르게 되면 iterator loop을 멈추고 그전까지 append한다. 
    + `partial`의 첫번째 인자인 함수에서 필요한 매개변수를 두번째 인자로 특정해준 버전의 함수를 새로 생성한다. 
 * Loop에서 다양한 break 포인트를 판별하는 법
    - 기존 방식
    ```py
        def find(seq, target):
            found = False
            for i, value in enumerate(seq):
                if value == target:
                    found = True
                    break
            if not found:
                return -1
            return i
    ```
    - Pythonic way
    ```py
        def find(seq, target):
            for i, value in enumerate(seq):
                if value == target:
                    break
            else:
                return -1

            return i
    ```

## Dictionary
 1. 딕셔너리를 잘 쓰는 것은 중요한 파이썬 스킬이다
 2. 관계, 링킹, 샘, 그룹화 시키는 데에 사용되는 주요 도구이다

 * 딕셔너리 Looping하는 법
    - 일반적인 방삭
    ```py
        d = {"Apple": 5, "Banana": 6, "Can": 3}

        for k in d:
            print k 
    ```
    - 기존 딕셔너리를 변화시켜야 할때
    ```py
        d = {"Apple": 5, "Banana": 6, "Can": 3}

        for k in d.keys():
            if k.startswith('C'):
                del d[k]
    ```
    - 한 줄 요약
    ```py
        d = {k: d[k] for k in d if not k.startswith('C')} 
    ```
    + 딕셔너리를 loop할 땐, 리스트에서 인덱스가 반환되는 것과 같은 논리로 키 값이 반환된다
    + `d.keys()`는 `d` 와 같은 딕셔너리를 새로 반환하여 딕셔너리의 값을 변환시킬 때 유용하다. 

 * 딕셔너리 키와 값이 동시에 필요한 loop
    - 일반 방식
    ```py
        for k in d:
            print k, '-->', d[k]
    ```
    - Pythonic way
    ```py
        for k, v in d.items():
            print k, '-->', v
    ```
    - Pythonic way + Efficiency 
    ```py
        for k, v in d.iteritems():
            print k, '-->', v
    ```
    + iteritems()는 iterator을 생성하기 때문에 items()보다 메모리 측면에서 효율적이다

  * 두 리스트를 페어로 딕셔너리로 만들기
    ```py
            names = ["Sean", "John", "Ben", "George"]
            colors = ["Red", "Blue", "Yellow", "Green"]
        ```
    - Pythonic way
    ```py
        d = dict(izip(names, colors))
    ```

  * 딕셔너리에서 개수 세기 
    ```py
        colors = ['red', 'green', 'red', 'blue', 'green', 'red']
    ```
    - 일반 방식
    ```py
        d = {}
        for color in colors:
            if color not in d:
                d[color] = 0
            d[color] += 1
    ```
    - dict.get()을 활용한 방식
    ```py
        d = {}
        for color in colors:
            d[color] = d.get(color, 0) + 1
    ```
    - collections.defaultdict를 활용한 방식
    ```py
        d = defaultdict(int)
        for color in colors:
            d[color] += 1
    ```
    + 첫번째 방식으로 짤줄 모르면 쉬워보인다고 `defaultdict`로 짜지 말자
    + `get()`은 두번째 인자로 기본값을 입력받는다.

  * 딕셔너리에서 그루핑하기 
    ```py 
        names = ['raymond', 'rachel', 'matthew', 'roger', 'betty', 'melissa', 'judith', 'charlie']
    ```
    - 일반 방식
    ```py
        d = {}
        for name in names:
            key = len(name)
            if key not in d:
                d[key] = []
            d[key].append(name)
    ```
    - setdefault()를 활용한 방식
    ```py
        d = {}
        for name in names:
            key = len(names)
            d.setdefault(key, []).append(name)
    ```
    - defaultdict()를 활용한 방식
    ```py
        d = defaultdict(list)
        for name in names:
            key = len(name)
            d[key].append(name)
    ```

 * popitem()은 원자성이 보장된다
    ```py
        while d:
            key, value = d.popitem()
            print key, '-->', value
    ```
    + 즉 쓰레드 사이에 lock을 걸 필요 없이 사용 가능하다


## 가독성
 1. 인덱스와 positional argument는 좋다
 2. 카워드와 이름은 더 좋다.
 3. 1.은 컴퓨터가 좋아하는 방식
 4. 2.는 인간이 생각하는 방식
 5. 컴퓨터에서 몇 마이크로초 빠르게 하겠다고 인간이 디버깅할 때 며칠걸리는 코드는 지양하자

 * Keyword argument를 활용한 가독성 높이기
    ```py
        twitter_search("@obama", False, 20, True)
    ```
    ```py
        twitter_search("@obama", retweets=False, numtweets=20, popular=True)
    ```
    
 * named tuple로 반환되는 값의 가독성 높이기
    ```py
        doctest.testmod()
        (0, 4)
    ```

    ```py
        TestResults = namedtuple('TestResults', ['failed', 'attempted'])
        doctest.testmod()
        TestResults(failed=0, attempted=4)
    ```

 * Unpacking sequences
    ```py
        p = 'Sean', 'Hong', 23, 'seanhong2000@myemail.com'
        fname = p[0]
        lname = p[1]
        age = p[2]
        email = p[3]
    ```

    ```py
        p = 'Sean', 'Hong', 23, 'seanhong2000@myemail.com'
        fname, lname, age, email = p
    ```

## 튜플
 1. 상태 변수를 업데이트 하는 것의 이점을 무시하지마라
 2. 순서가 어긋난 업데이트로 인한 다양한 에러를 제거할 수 있다
 3. 고차원적 사고를 가질 수 있다 "Chunking"

 * Concatenation
    - 비효율적 방식
    ```py
        names = ['raymond', 'rachel', 'matthew', 'roger', 'betty', 'melissa', 'judith', 'charlie']

        s = names[0]
        for name in names[1:]:
            s += ', ' + name
        print s
    ```
    - 효율적 방식
    ```py
        names = ['raymond', 'rachel', 'matthew', 'roger', 'betty', 'melissa', 'judith', 'charlie']
        print ', '.join(names)
    ```

 * Update sequence
    - 비효율적 방식
    ```py
        names = ['raymond', 'rachel', 'matthew', 'roger', 'betty', 'melissa', 'judith', 'charlie']

        del names[0]
        names.pop(0)
        names.insert(0, 'mark')
    ```
    - 효율적 방식
    ```py
        names = deque(['raymond', 'rachel', 'matthew', 'roger', 'betty', 'melissa', 'judith', 'charlie'])

        del names[0]
        names.popleft()
        names.appendleft('mark')
    ```

## 데코레이터
 1. 관리적 로직을 비지니스 로직으로부터 분리해라
 2. 코드를 고치고 코드 재사용성을 높이는 깔끔하고, 아름다운 도구
 3. 좋은 네이밍은 필수
 
 * 데코레이터로 관리적 로직을 제거하기
    - 일반적인 방법
    ```py
        def web_lookup(url, saved={}):
            if url in saved:
                return saved[url]
            page = urllib.urlopen(url).read()
            saved[url] = page
            return page
    ```

    - functools.cache 사용
    ```py
        @cache 
        def web_lookup(url):
            return urllib.urlopen(url).read()
    ```
    + cache decorator
    ```py
        def cache(func):
            saved = {}
            @wraps(func)
            def newfunc(*args):
                if args in saved:
                    return newfunc(*args)
                result = func(*args)
                saved[args] = result
                return result
            return newfunc
    ```

 * 일시적 context 무시하기
    - 일반 방식
    ```py
        old_context = getcontext().copy()
        getcontext().prec = 50
        print Decimal(355) / Decimal(113)
        setcontext(old_context)
    ```
    - localcontext(Context)활용
    ```py
        with localcontext(Context(prec=50)):
            print Decimal(355) / Decimal(113)
    ```

 * 파일 열고 닫기 
    ```py
        f = open('data.txt')
        try:
            data = f.read()
        finally:
            f.close()
    ```

    ```py
        with open('data.txt') as f:
            data = f.read()
    ```

 * Lock 사용하는 법
    - 예전 방식
    ```py
        lock = threading.Lock()

        lock.acquire()
        try:
            print 'Critical section1'
            print 'Critical section2'
        finally:
            lock.release()
    ```
    - 개선 방식
    ```py
        lock = threading.Lock()
        with lock:
            print 'Critical section1'
            print 'Critical section2'
    ```

 * temporary context 무시하기
    - 예전 방식
    ```py
        try:
            os.remove('somefile.tmp')
        except OSError:
            pass
    ```
    - ignored활용한 방식
    ```py
        with ignored(OSError):
            os.remove('somefile.tmp')
    ```

    - 예전 방식 (IO컨텍스트를 변경하는 법)
    ```py
        with open('help.txt', 'w') as f:
            oldstdout = sys.stdout
            sys.stdout = f 
            try: 
                help(pow)
            finally:
                sys.stdout = oldstdout
    ```
    - 데코레이터 활용
    ```py
        @contextmanager
        def redirect_stdout(fileobj):
            oldstdout = sys.stdout
            sys.stdout = fileobj
            try:
                yield fileobj
            finally
            sys.stdout = oldstdout


        with open('help.txt', 'w') as f:
            with redirect_stdout(f):
                help(pow)
    ```

## 원라이너
 1. 한줄에 너무 많이 넣지 마라
 2. 너무 세부적으로 나누지 마라
 3. 1과 2 동시에 둘다 어떻게 지키라고;;;

 * 예시
    - 긴 방식
    ```py
        result = []
        for i in range(10);
            s = i ** 2
            result.append(s)
        print sum(result)
    ```
    - 한 줄
    ```py
        print sum([i ** 2 for i in xrange(10)])
    ```
    - 한 줄 (generator 활용)
    ```py
        print sum(i ** 2 for i in xrange(10))
    ```
    + 세번째가 제일 빠르다
    + 한줄로 작성하는 기준은 사람마다 다르겠지만, 추천하는 이유는 긴 방식은 결과를 도출하는 방법을 하나하나을 보여준다면, 한 줄 방식은 해당 결과를 얻는 일관적인 사고를 갖고 있을 수 있다.


## 후기
50분짜리 영상을 정리하느라 애를 좀 먹을거 같았지만 1시간밖에 안걸렸다. 파이썬 내장함수로 구현된 함수들이 어떻게 구현되어 있는지, 기존 방식과 개선점과 차이는 뭐가 있는지를 설명해주니 왜 Pythonic 방식으로 사용해야되는지 알수 있게 되었다.
iterator, generator, decorator 등 파이썬을 작성할 때 메모리, 시작 복잡도면에서 효율적인 방식들로 생각하고 풀이. 가독성까지 챙기는 부분이 꼭 필요하다. 

예전 내가 작성해 놓은 코드를 다시 보면서 위 방식들을 도입해 재작성 해봐야겠다. 
