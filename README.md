# SWExpertAcademy_MockTest_Java_2382

## SW Expert Academy 2382. [모의 SW 역량테스트] 미생물 격리

### 1. 문제설명

출처: https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV597vbqAH0DFAVl

input으로 지도의 한변의 길이 `N`, 격리 시간 `M`, 미생물 군집의 개수 `K`이 들어온다. 이후 미생물에 대한 정보가 들어온다. 정보는 좌표와 미생물의 수, 이동방향이 온다. 지도상 테투리에는 약품이 처리되어있어 미생물 군집이 테두리에 닿으면 절반이 사망하며 방향을 반대로 바꾸어 이동한다. 같은 위치에 두개이상의 미생물 군집이 만나면 군집이 합쳐지며, 가장 미생물의 수가 많은 군집의 이동방향으로 이동하게된다. 이때 수가 같은 군집은 문제에서 주어지지 않는다. `M`시간 후 남아있는 미생물 수의 총 합을 출력하는 문제.


[제약사항]

    1. 시간제한 : 최대 50개 테스트 케이스를 모두 통과하는데, C/C++/Java 모두 5초
    2. 구역의 모양은 정사각형으로 주어지며, 한 변의 셀의 개수 N은 5이상 100이하의 정수이다. (5 ≤ N ≤ 100)
    3. 최초 배치되어 있는 미생물 군집의 개수 K는 5이상 1,000이하의 정수이다. (5 ≤ K ≤ 1,000)
    4. 격리 시간 M은 1이상 1,000이하의 정수이다. (1 ≤ M ≤ 1,000)
    5. 최초 약품이 칠해진 가장자리 부분 셀에는 미생물 군집이 배치되어 있지 않다.
    6. 최초에 둘 이상의 군집이 동일한 셀에 배치되는 경우는 없다.
    7. 각 군집 내 미생물 수는 1 이상 10,000 이하의 정수이다.
    8. 군집의 이동방향은 상하좌우 4방향 중 한 방향을 가진다. (상: 1, 하: 2, 좌: 3, 우: 4)
    9. 주어진 입력으로 진행하였을 때, 동일한 셀에 같은 미생물 수를 갖는 두 군집이 모이는 경우는 발생하지 않는다.
    10.  각 군집의 정보는 세로 위치, 가로 위치, 미생물 수, 이동 방향 순으로 주어진다. 각 위치는 0번부터 시작한다.


[입력]

> 첫 줄에는 총 테스트 케이스의 개수 `T`가 주어진다.
> 그 다음 줄부터 `T`개의 테스트 케이스가 차례대로 주어진다.
> 각 테스트 케이스의 첫째 줄에는 구역의 한 변에 있는 셀의 개수 `N`, 격리 시간 `M`, 미생물 군집의 개수 `K`가 순서대로 주어지며, 다음 `K`줄에 걸쳐서 미생물 군집 `K`개의 정보가 주어진다.
> 미생물 군집의 정보는 세로 위치, 가로 위치, 미생물 수, 이동 방향 순으로 4개의 정수가 주어진다.


[출력]

> 테스트 케이스의 개수 만큼 `T`개의 줄에 각 테스트 케이스에 대한 답을 출력한다.
> 각 줄은 `#x`로 시작하고, 공백을 하나 둔 후 정답을 출력한다. (`x`는 테스트 케이스의 번호이며, `1`번부터 시작한다.)
> 출력해야 할 정답은 `M`시간 후 남아 있는 미생물 수의 총 합이다.

### 2. 풀이

input에서 오는 미생물의 정보를 객체로 만들어 저장한다. 위치와 미생물의 수, 방향을 저장한 후,  미생물을 수가 가장 많은 순서로 정렬 후 하나씩 꺼내어 다음위치로 이동시키며 이때 테두리에 도착한다면 미생물의 수를 절반으로 줄인 후 이동방향을 뒤집는다. 하루가 지나면 마지막에 모든 미생물에 대해 같은 위치에 존재하는 군집이 있다면 수를 합쳐준다. 방향에 대해선 순차적으로 진행시 가장 숫자가 많은 군집의 방향이 먼저 그 자리를 차지한 군집이기 때문에 방향이아닌 숫자만 더해준다. 위작업을 `M`번 반복한 후 남은 군집에 대하여 미생물의 수를 더하여 출력하였다.

```java

public class Solution {

	static class Microbe {
		int x, y;
		int power;
		int dir;

		public Microbe(int x, int y, int power, int dir) {
			this.x = x;
			this.y = y;
			this.power = power;
			this.dir = dir;
		}
	}

	static int N, M, K;
	static int[][] map;
	static List<Microbe> microbes = new LinkedList<>();
	// (상: 1, 하: 2, 좌: 3, 우: 4)
	static int[][] dir = { { 0, 0 }, { -1, 0 }, { 1, 0 }, { 0, -1 }, { 0, 1 } };

	public static void main(String[] args) throws Exception {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

		int T = Integer.parseInt(br.readLine());

		for (int test_case = 1; test_case <= T; test_case++) {
			StringTokenizer st = new StringTokenizer(br.readLine());
			N = Integer.parseInt(st.nextToken());
			M = Integer.parseInt(st.nextToken());
			K = Integer.parseInt(st.nextToken());

			microbes.clear();

			for (int i = 0; i < K; i++) {
				st = new StringTokenizer(br.readLine());
				microbes.add(new Microbe(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()),
						Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken())));
			}
			
			for (int day = 0; day < M; day++) {
				int lIdx = 0;
				List<Microbe> lists = new ArrayList<>();
				map = new int[N][N];
				for (int i = 0; i < N; i++) {
					Arrays.fill(map[i], -1);
				}

				microbes.sort(new Comparator<Microbe>() {
					public int compare(Microbe m1, Microbe m2) {
						return m2.power - m1.power;
					}
				});
				for (int mIdx = 0; mIdx < microbes.size(); mIdx++) {
					Microbe m = microbes.get(mIdx);
					int curDir = m.dir;
					int nextX = m.x + dir[m.dir][0];
					int nextY = m.y + dir[m.dir][1];
					
					m.x = nextX;
					m.y = nextY;
					
					if (isEdge(nextX, nextY)) { // power절반, 방향 반대
						m.power/=2;
						m.dir = getReversedDir(curDir);
					}
					
					if (map[nextX][nextY] == -1) {
						lists.add(m);
						map[nextX][nextY] = lIdx++;
					} else {
						lists.get(map[nextX][nextY]).power += m.power;
					}
				}
				
				microbes.clear();
				microbes.addAll(lists);
			}
			
			System.out.println("#" + test_case + " "+microbes.stream().mapToInt(m -> m.power).sum());
		}
	}

	// (상: 1, 하: 2, 좌: 3, 우: 4)
	private static int getReversedDir(int curDir) {
		if (curDir%2 == 0)
			return curDir - 1;
		else 
			return curDir + 1;
	}

	private static boolean isEdge(int nextX, int nextY) {
		if (nextX == 0 || nextX == N - 1 || nextY == 0 || nextY == N - 1)
			return true;
		else
			return false;
	}

}


```



