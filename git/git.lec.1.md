# Git, Git-flow

## Git vs SVN

git 은 잦은 commit을 유도한다.

svn 사용자는 생각보다 commit을 잘 안한다. 이게 어떤 단점이 있는 것일까?
작업 범위가 크므로, 세밀한 추적이 어렵고, 결국 협업/업무백업이 어려워진다.

## 4. Git이란

속도 : svn은 모든 변화에 대한 추적을 하므로 느리다.
      git은 snapshot을 관리한다. git은 pointer를 옮긴다.
      모든 version에 대해서 snapshot을 찍어 관리한다.
      svn은 모든 변경점에 대한 변경관리를 모두 한다. 그래서 느리다.

파일 삭제를 하지 말고, soft reset을 유도 함. 그러나, reset hard도 있으나 권장하지는 않는다.
삭제는 굉장히 위험한것이다!!!

staging area??
일부만 정해서 반영 가능

근본적으로, commit하는 시점과 횟수가 다르다.
git에서는 단위작업이 끝나면 commit!

## .gitignore file

```bash
# 
/TODO

# 어디든 있는 build directory 무시
build/
```