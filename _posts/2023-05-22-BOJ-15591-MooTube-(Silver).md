---

layout: post
title: "[Java] 백준 15591번: MooTube (Silver)"
tags: [algorithm, baekjoon, java]

---

![15591](https://github.com/piacu/piacu.github.io/assets/26267376/afee938a-b76b-4b8d-90af-de99c0137d9a)



## 풀이 방법

영상(정점)까지 가는 경로는 **무조건** 존재하며 서로 연결되어 있는 N-1개의 쌍으로 나타내는 것을 보고 최소 스패닝 트리(MST)를 파악할 수 있었다. 인접 행렬로 입력값을 받은 다음 bfs를 사용해서 문제를 해결할 수 있었다.

## 풀이 코드

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class BOJ_15591_MooTube_Silver {
	static List<int[]>[] USADO;
	public static void main(String[] args) throws Exception {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(br.readLine());

		int N = Integer.parseInt(st.nextToken());
		int Q = Integer.parseInt(st.nextToken());

		USADO = new LinkedList[N+1];
		for(int i=1; i<N+1; i++) {
			USADO[i] = new LinkedList<>();
		}
		
		for(int i=0; i<N-1; i++) {
			st = new StringTokenizer(br.readLine());
			int p = Integer.parseInt(st.nextToken());
			int q = Integer.parseInt(st.nextToken());
			int r = Integer.parseInt(st.nextToken());
			
			USADO[p].add(new int[] {q, r});
			USADO[q].add(new int[] {p, r});
		}
		
		for(int i=0; i<Q; i++) {
			st = new StringTokenizer(br.readLine());
			int k = Integer.parseInt(st.nextToken());
			int v = Integer.parseInt(st.nextToken());
			
			findMooTube(k, v);
		}
	}
	private static void findMooTube(int k, int v) {
		Queue<Integer> q = new LinkedList<>();
		boolean[] visit = new boolean[USADO.length];
		q.add(v);
		visit[v] = true;
		int cnt = 0;
		
		while(!q.isEmpty()) {
			int currVtx = q.poll();
			
			for(int i=0; i<USADO[currVtx].size(); i++) {
				int oppoVtx = USADO[currVtx].get(i)[0];
				int oppoWeight = USADO[currVtx].get(i)[1];
				
				if(!visit[oppoVtx] && oppoWeight >= k) {
					q.add(oppoVtx);
					visit[oppoVtx] = true;
					cnt++;
				}
			}
		}
		System.out.println(cnt);
	}

}
```
