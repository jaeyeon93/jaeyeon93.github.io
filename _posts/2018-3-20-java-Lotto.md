---
layout: post
title: 2018-3-20 TIL Java 로또 Rank
---

## 로또 rank를 구현하기

```
import java.util.ArrayList;
import java.util.List;

public class Decision {
    static ArrayList<Rank> ranks = new ArrayList<>();
//    Check check = new Check();
    public ArrayList<Rank> decisionRank(List<String> rightNum, Lotto lotto, String bonusNum) {
        printLotto(lotto);
        int count = 0;
        for(int i = 0; i < rightNum.size(); i++) {
            if(lotto.getLotto().contains(Integer.parseInt(bonusNum))){
                count++;
                ranks.add(compareRank(lotto, count));
            }
        }
        return ranks;
    }

    public Rank compareRank(Lotto lotto, int count) {
        if(count == 5 && lotto.getLotto().contains(lotto.getLotto().get(5))) {
            System.out.println("second");
            return Rank.SECOND;
        }
        return Rank.valueOf(count, false);
        }

    public void printLotto(Lotto lotto) {
        System.out.print("first lotto is ");
        for(int i = 0; i < lotto.getLotto().size(); i++)
            System.out.print(lotto.getLotto().get(i) + " ");
        System.out.println();
    }
}

```
- 이전에는 lottos에 있는 lotto와 rightNum을 비교를 해서, 각 lotto별로 count의 값을 구해서 count를 통해서 값을 비교를 했다. 그러다보니 불필요한 문제들이 너무 많아서, Decision클래스를 통해서 각 로또별로 Rank값을 만드는 클래스를 만들었다. 로또의 경우 2등, 3등 구현하는데 bonusNum이 중요해서 compareRank메소드에 2등과 3등을 구분하는 메소드를 구분했다.

#### 오늘의 느낀점-
- scanner.nextLine()를 구현할때, 코드를 깔끔하게 하려고 main문에서 제외를 했었다. 그러다보니 check객체를 생성할때마다 계속해서 scanner.nextLine()이 호출이 되서 코드가 더 복잡해졌다. main문을 너무 줄이는것도 조심해야겠다. 이전에는 counts에서 count값들을 비교를 해서 상금과 맞춘갯수를 비교를 했는데, 2등을 구현하려고 하다보니까 복잡해져서 결국에는 Rank를 결정하고 Rank를 리턴하는 메소드를 구현을 해서 Rank값을 통해서 상금을 받고 카운팅을 하는 방식으로 구현중에 있다.
