> 팀원분들과 소통이 이루어지지 않아 개인과제로 진행하였습니다. 이 과제에 다른 팀원 두 분은 참여하지 않으셨습니다.
# 201902954한현진_HuffmanCoding
## 허프만 압축 
> 문서에서 발생 빈도가 높은 문자에는 짧은 부호를 할당하고, 발생 빈도가 낮은 문자에는 긴 부호를 할당하여 효율을 높이는 압축 기법이다.
> 모든 문자에 서로 다른 prefix code가 할당되어 코드와 코드 사이가 구분된다.

## 구현 과정
1) 문서/문자열의 각각의 문자들의 발생 빈도를 구하여 오름차순으로 정렬한다
2) 가장 발생 빈도가 작은 2개의 문자를 자식 노드로 하고, 그 2개의 문자의 발생 빈도수의 합을 부모 노드로 한다
3) 모든 문자들이 트리에 삽입될 때까지 반복하여 허프만 트리를 만든다
4) 각각의 노드의 왼쪽 자식에는 0, 오른쪽 자식에는 1을 할당
5) 각각의 문자들에 root 노드부터 해당 노드까지 할당된 비트들을 연결하여 prefix code로 할당

## 코드 구현
> 1. 발생 빈도의 오름차순 정렬을 위한 우선순위 큐와 허프만 트리를 만들 노드
``` java
class Node implements Comparable<Node> {
     int freq;//발생 빈도
     char c;
     Node left;
     Node right;

    public int compareTo(Node n) { //노드들을 우선순위 큐에 삽입하기 위해 발생빈도가 적은 것을 우선순위로 정해줌
        return freq - n.freq;
    }
}
```
> 2. 문자들의 발생 빈도수를 계산합니다
 
``` java
// 1) 데이터를 읽으면서 문자들의 발생 빈도수를 계산
        c_freq = new HashMap<Character, Integer>();
        for (int i = 0; i < data.length(); i++) {
            char thisC = data.charAt(i);
            //이 문자가 c_freq에 이미 있으면 +1 해줌 / 없으면 이제 1로 삽입
            if (c_freq.containsKey(thisC)) {
                c_freq.put(thisC, c_freq.get(thisC) + 1);
            } else c_freq.put(thisC, 1);
        }
```
> 3. 우선순위 큐를 사용하여 문자들을 발생 빈도순으로 정렬합니다
``` java
 // 2) 우선순위 큐 생성
        chars = new PriorityQueue<Node>();
        int count = 0;
        for (Character a : c_freq.keySet()) {
            Node n = new Node();
            n.c = a;
            n.freq = c_freq.get(a);
            chars.add(n);
            count++;
        } // 문자들이 발생 빈도순으로 정렬됨
```
> 4. 우선순위 큐를 사용하여 허프만 트리를 만듭니다
``` java
 public static Node huffmanTree(int n) { //prefix code를 할당할 수 있도록 허프만 트리를 만듭니다
        for(int i=0;i<n-1;i++) {
            Node h = new Node();//h = 허프만 노드임
            h.right = chars.poll(); //빈도수가 가장 작은 문자를 우선순위 큐에서 꺼내 오른쪽에 먼저 할당합니다(왼쪽 자식노드의 값 > 오른쪽 자식노드의 값)
            h.left = chars.poll();
            h.freq = h.right.freq+h.left.freq; //부모 노드에는 왼쪽과 오른쪽 자식의 빈도 수의 합을 할당합니다
            chars.add(h);
        } //문자들의 발생 빈도 오름차순으로 정렬되어 있던 우선순위 큐에서 노드를 꺼내와서 허프만 트리 형태로 만들어준 뒤 다시 삽입해주었습니다
        return chars.poll();
    }
```
> 5. 허프만 트리에 prefix code를 할당합니다
``` java
public static void prefixCode(Node h, String pfcode){ //각각의 문자들에 prefix code를 할당합니다
        if (h==null) return;
        //루트에서부터 왼쪽 자식 노드에는 0, 오른쪽 자식 노드에는 1을 할당
        else{
            prefixCode(h.left, pfcode + '0');
            prefixCode(h.right, pfcode + '1');
        }
```
> 6. main - 허프만 압축된 결과물을 출력합니다
``` java
        String huffmancoding = new String(); //허프만 압축된 결과물
        for (int i = 0; i < data.length(); i++) {
            huffmancoding = huffmancoding + huffmanCode.get(data.charAt(i));//문자 -> 각 문자들의 prefix code
        }
        // 5) 허프만 압축된 결과물 출력
        System.out.println(huffmancoding);
```
## 전체 코드
``` java
//201902954 한현진
import java.nio.charset.StandardCharsets;
import java.util.*;

// 허프만 트리의 노드
class Node implements Comparable<Node> {
     int freq;//발생 빈도
     char c;
     Node left;
     Node right;

    public int compareTo(Node n) { //노드들을 우선순위 큐에 삽입하기 위해 발생빈도가 적은 것을 우선순위로 정해줌
        return freq - n.freq;
    }
}

public class Huffman {
    public static HashMap<Character, Integer> c_freq = new HashMap<Character, Integer>(); //문자들과 그 문자들 각각의 발생 빈도를 카운트하여 저장해 놓을 C_freq
    public static PriorityQueue<Node> chars; //입력되는 문서의 문자들을 발생 빈도순으로 정렬하는 우선순위 큐
    public static HashMap<Character,String> huffmanCode = new HashMap<Character, String>(); //문자들과 문자마다 각각의 prefix code들을 저장

    public static Node huffmanTree(int n) { //prefix code를 할당할 수 있도록 허프만 트리를 만듭니다
        for(int i=0;i<n-1;i++) {
            Node h = new Node();//h = 허프만 노드임
            h.right = chars.poll(); //빈도수가 가장 작은 문자를 우선순위 큐에서 꺼내 오른쪽에 먼저 할당합니다(왼쪽 자식노드의 값 > 오른쪽 자식노드의 값)
            h.left = chars.poll();
            h.freq = h.right.freq+h.left.freq; //부모 노드에는 왼쪽과 오른쪽 자식의 빈도 수의 합을 할당합니다
            chars.add(h);
        } //문자들의 발생 빈도 오름차순으로 정렬되어 있던 우선순위 큐에서 노드를 꺼내와서 허프만 트리 형태로 만들어준 뒤 다시 삽입해주었습니다
        return chars.poll();
    }
    public static void prefixCode(Node h, String pfcode){ //각각의 문자들에 prefix code를 할당합니다
        if (h==null) return;
        //루트에서부터 왼쪽 자식 노드에는 0, 오른쪽 자식 노드에는 1을 할당
        else{
            prefixCode(h.left, pfcode + '0');
            prefixCode(h.right, pfcode + '1');
        }
        if (h.c !='\0') {
            System.out.println(h.c + "=" + pfcode);
            huffmanCode.put(h.c, pfcode); //문자들과 그 문자의 prefix code들을 저장
        }
    }

    public static void main(String[] args) {
        String data = new Scanner(System.in).nextLine();

        // 1) 데이터를 읽으면서 문자들의 발생 빈도수를 계산
        c_freq = new HashMap<Character, Integer>();
        for (int i = 0; i < data.length(); i++) {
            char thisC = data.charAt(i);
            //이 문자가 c_freq에 이미 있으면 +1 해줌 / 없으면 이제 1로 삽입
            if (c_freq.containsKey(thisC)) {
                c_freq.put(thisC, c_freq.get(thisC) + 1);
            } else c_freq.put(thisC, 1);
        }

        // 2) 우선순위 큐 생성
        chars = new PriorityQueue<Node>();
        int count = 0;
        for (Character a : c_freq.keySet()) {
            Node n = new Node();
            n.c = a;
            n.freq = c_freq.get(a);
            chars.add(n);
            count++;
        } // 문자들이 발생 빈도순으로 정렬됨

        // 3) 모든 노드에 대해서 허프만 트리로 만들어줍니다
        Node root = huffmanTree(count);
        // 4) 허프만 트리의 루트 노드에서부터 prefix code 할당하여 모든 노드에 대해 prefix code를 할당합니다
        prefixCode(root, new String());

        String huffmancoding = new String(); //허프만 압축된 결과물
        for (int i = 0; i < data.length(); i++) {
            huffmancoding = huffmancoding + huffmanCode.get(data.charAt(i));//문자 -> 각 문자들의 prefix code
        }
        // 5) 허프만 압축된 결과물 출력
        System.out.println(huffmancoding);

        //성능 분석을 위한 코드
        int size_before = data.getBytes(StandardCharsets.UTF_8).length;
        System.out.println("허프만 코딩 전 문자열 크기 = " + size_before * 8);
        System.out.println("허프만 코딩 후 문자열 크기 = " + huffmancoding.length());
    }
}

```
## 성능 분석
> 
``` java
//성능 분석을 위한 코드
        int size_before = data.getBytes(StandardCharsets.UTF_8).length;
        System.out.println("허프만 코딩 전 문자열 크기 = " + size_before * 8);
        System.out.println("허프만 코딩 후 문자열 크기 = " + huffmancoding.length());
```
>![5651](https://user-images.githubusercontent.com/80517119/163231593-65fb79bd-1b3b-404a-8656-167dbaa75e99.JPG)
![image](https://user-images.githubusercontent.com/80517119/163235282-57764eaa-286c-47fc-b9f5-26f2fd39cc47.png)

> - 같은 문자가 반복될수록 성능이 좋습니다
--------------
>![22](https://user-images.githubusercontent.com/80517119/163231742-414952c9-bffb-4d50-bc1c-c39e5a9d74df.JPG)
>![33](https://user-images.githubusercontent.com/80517119/163231773-2facdb91-6f4f-4a6f-9d74-be10a11295b2.JPG)
> - 데이터의 길이가 길어질수록 원 데이터의 약 50%로 압축됩니다
