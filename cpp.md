#include<bits/stdc++.h>
using namespace std;

void bfsIterative(string startCity, unordered_map<string,vector<string>> &adj) {

    queue<string> q;
    # C++ & HTML Snippets — formatted with copy buttons

    Below are the original code snippets grouped into sections. Each section has a "Copy" button that copies the exact code block to your clipboard. I did not modify the code content — only added headings and copy buttons.

    <!-- Copy helper script -->
    <script>
    function copyFollowingCode(btn) {
      var el = btn.nextElementSibling;
      while (el && el.tagName !== 'PRE') el = el.nextElementSibling;
      if (!el) { alert('Code block not found'); return; }
      var codeEl = el.querySelector('code') || el;
      var text = codeEl.innerText || codeEl.textContent;
      navigator.clipboard.writeText(text).then(function(){
        var old = btn.innerText;
        btn.innerText = 'Copied!';
        setTimeout(function(){ btn.innerText = old; }, 2000);
      }, function(err){ alert('Copy failed: ' + err); });
    }
    </script>

    ## 1) Graph Traversals (BFS / DFS)

    <button onclick="copyFollowingCode(this)">Copy</button>
    ```cpp
    #include<bits/stdc++.h>
    using namespace std;

    void bfsIterative(string startCity, unordered_map<string,vector<string>> &adj) {

        queue<string> q;
        unordered_set<string> visited;

        visited.insert(startCity);
        q.push(startCity);

        cout << "BFS Traversal Iterative : \n\n";

        while(!q.empty()) {
            string city = q.front();
            q.pop();

            cout << city << " -> ";

            for(string neighbour : adj[city]) {
                if(visited.find(neighbour) == visited.end()) {
                    q.push(neighbour);
                    visited.insert(neighbour);
                }
            }
        }

        cout << "\n ------------------\n\n";
    }


    void dfsRecursiveUtil(string curr, unordered_set<string> &visited, unordered_map<string, vector<string>> &adj) {
        visited.insert(curr);
        cout << curr << " --> ";

        for(string neighbour : adj[curr]) {
            if(visited.find(neighbour) == visited.end()) {
                dfsRecursiveUtil(neighbour, visited, adj);
            }
        }
    }

    void dfsRecursive(string startCity, unordered_map<string, vector<string>> & adj) {

        unordered_set<string> visited;
        cout << "Recursive DFS Traversal (Deep Exploration):\n";
        dfsRecursiveUtil(startCity, visited, adj);

        cout << "\n ------------------\n\n";
    }



    void bfsRecursiveUtil(queue<string> &q, unordered_set<string> &visited, unordered_map<string, vector<string>> &adj) {
        // base case
        if(q.empty()) {
            return ;
        }

        string curr = q.front();

        q.pop();

        cout << curr << " --> ";

        for(string neighbour : adj[curr]) {
            if(visited.find(neighbour) == visited.end()) {

                visited.insert(neighbour);

                q.push(neighbour);
            }
        }
    
        bfsRecursiveUtil(q, visited, adj);
    }


    void bfsRecursive(string startCity, unordered_map<string, vector<string>> &adj) {
        unordered_set<string> visited;
        queue<string> q;
        q.push(startCity);
        visited.insert(startCity);

        cout << "Recursive BFS Traversal (City Network):\n";
        bfsRecursiveUtil(q, visited, adj);

        cout << "\n ------------------\n\n";
    }

    void dfsIterative(string startCity, unordered_map<string, vector<string>> &adj) {
        unordered_set<string> visited;

        stack<string> stk;

        stk.push(startCity);
        cout << "Iterative DFS Traversal:\n";

        while(!stk.empty()) {
            string curr = stk.top();
            stk.pop();

            if(visited.find(curr) == visited.end()) {
                cout << curr << " -> ";
                visited.insert(curr);
            }

            for(auto it = adj[curr].rbegin(); it != adj[curr].rend(); it++) {
                if(visited.find(*it) == visited.end()) {
                    stk.push(*it);
                }
            }
        }
    }

    int main() {
        unordered_map<string, vector<string>> flightMap;

        // Creating a sample European Flight Network
        flightMap["London"] = {"Paris", "Berlin", "Dublin"};
        flightMap["Paris"] = {"London", "Madrid", "Rome"};
        flightMap["Berlin"] = {"London", "Prague", "Warsaw"};
        flightMap["Dublin"] = {"London"};
        flightMap["Madrid"] = {"Paris"};
        flightMap["Rome"] = {"Paris", "Athens"};
        flightMap["Prague"] = {"Berlin"};
        flightMap["Warsaw"] = {"Berlin"};
        flightMap["Athens"] = {"Rome"};

        cout << "=== FLIGHT ROUTING SYSTEM ===\n\n";
    
        bfsIterative("London", flightMap);
        dfsRecursive("London", flightMap);
        dfsIterative("London", flightMap);

        bfsRecursive("London", flightMap);
    }
    ```

    ## 2) A* Pathfinding (grid)

    <button onclick="copyFollowingCode(this)">Copy</button>
    ```cpp
    #include<bits/stdc++.h>
    using namespace std;


    int dx[] = {-1, 0, 1, 0};
    int dy[] = {0, 1, 0, -1};


    int heuristic(int x, int y, int destX, int destY) {
        return abs(destX - x) + abs(destY - y);
    }

    void aStar(vector<vector<int>> &grid, int n, int m, int startX, int startY, int destX, int destY) {

        if(startX < 0 || startX >= n || startY < 0 || startY >= m || destX < 0 || destX >= n || destY < 0 || destY >= m || grid[startX][startY] == 1 || grid[destX][destY] == 1) {

            cout << "Invalid start or destination (Out of bounds or on an obstacle)." << endl;
            return ;
        }

        priority_queue<pair<int,pair<int,int>>, vector<pair<int,pair<int,int>>>, greater<pair<int,pair<int,int>>>> pq;

        vector<vector<int>> g(n, vector<int>(m, 1e9)); // basically this is the distance array thay stores the actual distance from src to grid cell (i, j)

        vector<vector<pair<int,int>>> parent(n, vector<pair<int,int>>(m, {-1, -1}));  //  

        pq.push({heuristic(startX, startY, destX, destY), {startX, startY}}); // f = g + h --> here g = 0 for start point so --> f = h

        g[startX][startY] = 0;

        while(!pq.empty()) {

            auto it = pq.top();
            pq.pop();
            int x = it.second.first;
            int y = it.second.second;

            if(x == destX && y == destY) {
                // path found
                vector<pair<int,int>> path;
                while(x != -1 && y != -1) {
                    path.push_back({x, y});
                    int px = parent[x][y].first;
                    int py = parent[x][y].second;
                    x = px;
                    y = py;
                }


                reverse(path.begin(), path.end());

                cout << "\nPath Found!\n";
                cout << "Cost (Steps): " << g[destX][destY] << "\n";

                cout << "Coordinates: ";

                for(auto p : path) {
                    cout << "(" << p.first << "," << p.second << ") ";
                }
                cout<<endl;
                return ;
            }

            for(int i = 0; i < 4; i++) {

                int nx = x + dx[i];
                int ny = y + dy[i];

                if(nx >= 0 && nx < n && ny >= 0 && ny < m && grid[nx][ny] == 0) {
                    int newG = g[x][y] + 1;

                    if(newG < g[nx][ny]) {

                        g[nx][ny] = newG;

                        parent[nx][ny] = {x, y};

                        int hVal = heuristic(nx, ny, destX, destY);

                        int fVal = hVal + newG;

                        pq.push({fVal, {nx, ny}});

                    }
                }
            }

        }


        cout << "\nPath not found: The destination is completely blocked off." << endl;

    }


    int main() {

        int n,m;
        cout << "Enter rows and columns: ";
        cin >> n >> m;

        vector<vector<int>> grid(n, vector<int>(m));

        cout << "Enter the grid (0 for path, 1 for obstacle): \n";

        for(int i = 0; i < n; i++) {

            for(int j = 0; j < m; j++) {

                cin >> grid[i][j];
            }
        }

        int startX, startY, destX, destY;

        cout << "Enter start coordinates X and Y : ";
        cin >> startX >> startY;

        cout << "Enter destination coordinates X and Y : ";
        cin >> destX >> destY;

        aStar(grid, n, m, startX, startY, destX, destY);
    }
    ```

    ## 3) Minimum Spanning Tree

    <button onclick="copyFollowingCode(this)">Copy</button>
    ```cpp
    #include<bits/stdc++.h>
    using namespace std;


    void minimumSpanningTree(vector<vector<pair<int,int>>> adj, int n) {

        vector<int> parent(n, -1);

        int mstCost = 0;

        priority_queue<vector<int>, vector<vector<int>>, greater<vector<int>>> pq;

        pq.push({0, 0, -1});

        vector<int> vis(n, 0);

        while(!pq.empty()) {

            auto it = pq.top();
            pq.pop();

            int cost = it[0];
            int node = it[1];
            int parentNode = it[2];

        
            if(vis[node] == 1) continue;
        
            vis[node] = 1;

            parent[node] = parentNode;

            mstCost+=cost;

            for(auto it : adj[node]) {
                int adjNode = it.first;
                int adjWeight = it.second;

                if(!vis[adjNode]) {
                    // lets take this
                    pq.push({adjWeight, adjNode, node});
                }
            }
        }


        cout << "Total Cost of MST: " << mstCost << "\n\n";
        cout << "Edges in the Minimum Spanning Tree:\n";
    
    
        for(int i = 0; i < n; i++) {
            if(parent[i] != -1) {
                cout << "Node " << parent[i] << " - Node " << i << "\n";
            }
        }
        //return mstCost;
    }


    int main() {

        int n = 5; // Number of vertices
        vector<vector<pair<int,int>>> adj(n);

        // Creating a sample weighted undirected graph
        // Format: adj[u].push_back({v, weight});
        adj[0].push_back({1, 2});
        adj[0].push_back({3, 6});
    
        adj[1].push_back({0, 2});
        adj[1].push_back({2, 3});
        adj[1].push_back({3, 8});
        adj[1].push_back({4, 5});
    
        adj[2].push_back({1, 3});
        adj[2].push_back({4, 7});
    
        adj[3].push_back({0, 6});
        adj[3].push_back({1, 8});
    
        adj[4].push_back({1, 5});
        adj[4].push_back({2, 7});

        minimumSpanningTree(adj, n);

    } 
    ```

    ## 4) Dijkstra (shortest paths)

    <button onclick="copyFollowingCode(this)">Copy</button>
    ```cpp
    #include<bits/stdc++.h>
    using namespace std;


    void dijkstra(int n, vector<vector<pair<int,int>>> &adj, int source, int dest = -1) {

        vector<int> dist(n, INT_MAX);

        priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> pq;

        pq.push({0, source});

        vector<int> path(n);
        for(int i = 0; i < n; i++) {
            path[i] = i;
        }

        dist[source] = 0;

        while(!pq.empty()) {
            auto pr = pq.top();
            pq.pop();
            int cost = pr.first;
            int node = pr.second;

            if(cost > dist[node]) continue;

            for(auto it : adj[node]) {
                int adjNode = it.first;
                int adjWt = it.second;

                if(cost + adjWt < dist[adjNode]) {
                    dist[adjNode] = cost + adjWt;
                    pq.push({dist[adjNode], adjNode});
                    path[adjNode] = node;
                }
            }
        }

        if(dest == -1) {
            cout << "Cheapest Cost to reach all nodes from node " << source << endl;
            for(int i = 0; i < n; i++) {
            
                cout << "Distance to node " << i << " : " << dist[i] << endl; 
            }
            cout<<"\n\n";
            return ;
        }


        if (dist[dest] == INT_MAX) {
            cout << "Node " << dest << " is UNREACHABLE from node " << source << "\n\n";
            return;
        }
    
        cout << "Chepest cost from " << source << " to " << dest << " : " << dist[dest] << "\n\n";

        vector<int> track;
        int node = dest;
        while(node != source) {
            track.push_back(node);
            node = path[node];
        }
        track.push_back(source);
        reverse(track.begin(), track.end());
        for(int i = 0; i < track.size()-1; i++) {
            cout << track[i] << " ---- ";
        }
        cout << track[track.size()-1] << "\n\n\n";
    }


    int main() {
        int n = 6; 
        vector<vector<pair<int,int>>> adj(n);

        // Sample Graph
        adj[0].push_back({1, 4});
        adj[0].push_back({2, 4});
        adj[1].push_back({0, 4});
        adj[1].push_back({2, 2});
        adj[2].push_back({0, 4});
        adj[2].push_back({1, 2});
        adj[2].push_back({3, 3});
        adj[2].push_back({4, 1});
        adj[2].push_back({5, 6});
        adj[3].push_back({2, 3});
        adj[3].push_back({5, 2});
        adj[4].push_back({2, 1});
        adj[4].push_back({5, 3});
        adj[5].push_back({2, 6});
        adj[5].push_back({3, 2});
        adj[5].push_back({4, 3});

        // Test 1: Find shortest path from 0 to 5
        dijkstra(n, adj, 0, 5);

        // Test 2: Print distances to all nodes from 0
        dijkstra(n, adj, 0);


        return 0;
    }
    ```

    ## 5) N-Queens (Backtracking & Branch-and-Bound)

    <button onclick="copyFollowingCode(this)">Copy</button>
    ```cpp
    #include<bits/stdc++.h>
    #include<unistd.h>
    using namespace std;

    // Backtracking version

    bool isSafe(int row, int col, vector<string> &board, int n) {

        // check for three directions 
    
        // uppper left diagonal
        for(int stR = row, stC = col; stR >= 0 && stC >= 0; stR--, stC--) {
            if(board[stR][stC] == 'Q') 
                return false;
        }

        // upper right diagonal
        for(int stR = row, stC = col; stR >= 0 && stC < n; stR--, stC++) {
            if(board[stR][stC] == 'Q') return false;
        }

        // same row check upside
        for(int rS = row; rS >= 0; rS--) {
            if(board[rS][col] == 'Q') return false;
        }
        return true;
    }



    void nqueensBackTrack(int row, vector<vector<string>> &ans, vector<string> &board, int n) {
        if(row == n) {
            ans.push_back(board);
            return ;
        }

        for(int col = 0; col < n; col++) {

            if(isSafe(row, col, board, n)) {
                board[row][col] = 'Q';
                nqueensBackTrack(row+1, ans, board, n);
                board[row][col] = '.';
            }
        }
    }


    vector<vector<string>> solveNQueensBackTracking(int n) {
        vector<vector<string>> ans;
        vector<string> board(n, string(n, '.'));

        nqueensBackTrack(0, ans, board, n);

        return ans;
    }




    // Branch and bound version

    void nqueensBranchAndBound(int row, vector<string> &board, vector<vector<string>> &ans, int n, vector<int> &leftDiagonal, vector<int> &rightDiagonal, vector<int> &upperColumn) {
        if(row == n) {
            ans.push_back(board);
            return ;
        }
        //
        for(int col = 0; col < n; col++) {
            if(leftDiagonal[row - col + n - 1] == 0 && rightDiagonal[row + col] == 0 && upperColumn[col] == 0) {

                board[row][col] = 'Q';

                leftDiagonal[row - col + n - 1] = 1;
                rightDiagonal[row + col] = 1;
                upperColumn[col] = 1;

                nqueensBranchAndBound(row+1, board, ans, n, leftDiagonal, rightDiagonal, upperColumn);
            
                board[row][col] = '.';
                leftDiagonal[row - col + n - 1] = 0;
                rightDiagonal[row + col] = 0;
                upperColumn[col] = 0;
            }
        }
    }



    vector<vector<string>> nqueensUsingBranchAndBound(int n) {
        vector<vector<string>> ans;

        vector<string> board(n, string(n, '.'));

        // for left diagonal, right diagonal and columns
        vector<int> leftDiagonal(2 * n - 1, 0), rightDiagonal(2 * n-1, 0), upperColumn(n, 0);


        nqueensBranchAndBound(0, board, ans, n, leftDiagonal, rightDiagonal, upperColumn);

        return ans;
    }


    int main() {
        int n;
        cout << "Enter n: ";
        cin >> n;

        vector<vector<string>> ans;
        ans = nqueensUsingBranchAndBound(n);

        for(int i = 0; i < ans.size(); i++) {
            // now we have got the row*col matrix
            for(int j = 0; j < ans[i].size(); j++) {
                for(int k = 0; k < ans[i][j].size(); k++) {
                    cout << ans[i][j][k] << " ";
                    //sleep(1);
                }
                cout<<endl;
            }
                
            cout << "\n\n-----------------------------\n\n";
            sleep(2);
        }
    }
    ```

    ## 6) Expert System & Utilities (CLI)

    <button onclick="copyFollowingCode(this)">Copy</button>
    ```cpp
    #include<bits/stdc++.h>
    using namespace std;

    string toLowerCase(string str) {
        transform(str.begin(), str.end(), str.begin(), ::tolower);
        return str;
    }


    void diagnose() {
        cin.ignore();

        cout << "\nDescribe your symptoms (e.g., 'I have fever, cough, and feel tired'):\n";

        string input;

        getline(cin, input);

        input = toLowerCase(input);

        bool fever = input.find("fever") != string::npos;
        bool cough = input.find("cough") != string::npos;
        bool headache = input.find("headache") != string::npos || input.find("head pain") != string::npos;
        bool bodyPain = input.find("bodypain") != string::npos || input.find("body pain") != string::npos;
        bool fatigue = input.find("fatigue") != string::npos || input.find("weakness") != string::npos || input.find("tired") != string::npos;
        bool vomiting = input.find("vomiting") != string::npos;
        bool looseMotion = input.find("loosemotion") != string::npos || input.find("loose motion") != string::npos || input.find("loose motions") != string::npos || input.find("diarrhea") != string::npos;
        bool sneezing = input.find("sneezing") != string::npos || input.find("sneeze") != string::npos;
        bool rash = input.find("rash") != string::npos || input.find("rashes") != string::npos;

        cout << "\n================ DIAGNOSIS REPORT ================\n";

        if(fever && cough && fatigue && headache) { // fcfh
            cout << "Disease: COVID-19 SYMPTOMS\n";
            cout << "Medicine: Paracetomol, Vitamin C, Zinc\n";
            cout << "Advice: Stay isolated and continuously monitor oxygen levels.\n";
        }
        else if(fever && cough && fatigue) { // fcf
            cout << "Disease: INFLUENZA (FLU)\n";
            cout << "Medicine: Paracetomol, Cough Syrup\n";
            cout << "Advice: Take bed rest immediately and try to drink warm fluids.\n";
        }   
        else if(fever && bodyPain && headache) {
            cout << "Disease: VIRAL FEVER\n";
            cout << "Medicine: Paracetomol, Ibuprofen\n";
            cout << "Advice: Check temperature at regular intervals and stay hydrated.\n";
        }
        else if(fever && bodyPain && fatigue) {
            cout << "Disease: DENGUE (POSSIBLE)\n";
            cout << "Medicine: Paracetomol, Avoid taking Ibuprofen or Aspirin\n";
            cout << "Advice: Get a blood test done as soon as possible and monitor platelet counts\n";
        }
        else if(vomiting && looseMotion) {
            cout << "Disease: FOOD POISONING / GASTROENTERITIS\n";
            cout << "Medicine: ORS(Oral Rehydration Salts), Electrolytes\n";
            cout << "Advice: Avoid eating solid food for few hours and stay hydrated.\n";
        }
        else if(!fever && cough) {
            cout << "Disease: COMMON COLD\n";
            cout << "Medicine: Antihistamines, Lozenges\n";
            cout << "Advice: Avoid drinking cold drink and allergens.\n";
        } 
        else if(!fever && headache) {
            cout << "Disease: MIGRAINE / TENSION HEADACHE\n";
            cout << "Medicine: Pain Relievers\n";
            cout << "Advice: Rest in a dark quiet room away from screens.\n";
        }
        else if(sneezing && !fever && !cough) {
            cout << "Disease: ALLERGIC RHINITIS\n";
            cout << "Medicine: Cetirizine (Antihistamines)\n";
            cout << "Advice: Identify and avoid dust, pollution, or pollen.\n";
        }
        else if(rash) {
            cout << "Disease: SKIN ALLERGY / DERMATITIS\n";
            cout << "Medicine: Antihistamines, Calamine Lotion\n";
            cout << "Advice: Avoid rubbing and scratching affected part; wash with cool water.\n";
        }
        else {
            cout << "Disease: UNKNOWN CONTEXT\n";
            cout << "Medicine: No Medication Prescribed Dynamically\n";
            cout << "Advice: Symptoms do not match the current rule base\n";
            cout << "Please Consult a qualified practitioner.\n";
        }

        cout << "================================================================\n";
    }



    void routeDepartments() {
        cin.ignore();
    

        cout << "Describe the core medical issue or area of pain (e.g. 'chest pain', 'child has fever'): \n";

        string input;

        getline(cin, input);

        input = toLowerCase(input);


        bool cardiac = input.find("cardiac") != string::npos || input.find("heart") != string::npos || input.find("chest") != string::npos || input.find("chest pain") != string::npos || input.find("stroke") != string::npos;
        bool bone = input.find("bone") != string::npos || input.find("fracture") != string::npos || input.find("accident") != string::npos || input.find("joint") != string::npos;
        bool child = input.find("child") != string::npos || input.find("infant") != string::npos || input.find("baby") != string::npos;
        bool skin = input.find("skin") != string::npos || input.find("allergy") != string::npos || input.find("rash") != string::npos || input.find("itching") != string::npos;
        bool neuro = input.find("paralysis") != string::npos || input.find("fainting") != string::npos;

        cout << "\n================ TRIAGE & ROUTING SELECTION ================\n";

        if(cardiac || neuro) {
            cout << "Emergency Level: CRITICAL / RED ALERT\n";
            cout << "Recommended Unit: EMERGENCY ER / CARDIAC ICU\n";
            cout << "Action Required: Bypass standard registration process, move patient to trauma bay immediately.\n";
        }
        else if(bone) {
            cout << "Emergency Level: URGENT\n";
            cout << "Recommended Unit: ORTHOPEDICS DEPARTMENT (OPD ROOM 202)\n";
            cout << "Action Required: Move patient for X-ray imaging unit prior to specialist evaluation.\n";
        }
        else if(child) {
            cout << "Emergency Level: STANDARD\n";
            cout << "Recommended Unit: PEDIATRICS WING (OPD ROOM NO 302)\n";
            cout << "Action Required: Check child's vital parameters at the desk.\n";
        }
        else if(skin) {
            cout << "Emergency Level: STANDARD\n";
            cout << "Recommended Unit: DERMATOLOGY CLINIC (OPD ROOM NO 506)\n";
            cout << "Action Required: Standard OPD Token queue generation\n";
        }
        else {
            cout << "Emergency Level: GENERAL ENQUIRY\n";
            cout << "Recommended Unit: GENERAL MEDICINE OUTPATIENT\n";
            cout << "Action Required: Collect physical token from the reception and wait in the queue.\n";
        }
    }


    int main() {

        int choice;
        do {
            cout << "\n===== HOSPITALS AND MEDICAL FACILITIES EXPERT SYSTEM =====\n";
            cout << "1. Diagnose Symptoms (Diagnostic Engine)\n";
            cout << "2. Department Routing and Triage (Operational Engine)\n";
            cout << "3. Exit\n\n";

            cout << "Enter your choice: ";
            cin >> choice;

            switch(choice) {
                case 1:
                    diagnose();
                    break;
                case 2:
                    routeDepartments();
                    break;
                case 3:
                    cout << "Exiting System\n";
                    break;
                default:
                    cout << "Invalid choice, Please try again\n\n";
            }
        } while(choice != 3);
    }
    ```

    ## 7) Simple Gym Chatbot (HTML)

    <button onclick="copyFollowingCode(this)">Copy</button>
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Simple Gym Chatbot</title>
        <style>
            /* Centers the entire content on the page */
            body {
                text-align: center;
                padding-top: 50px;
                background-color: #f5f5f5; /* Light gray background for the whole page */
                font-family: sans-serif;
            }

            /* Bare minimum setup for the chat box */
            #chat-box {
                width: 300px;
                height: 300px;
                border: 1px solid black;
                overflow-y: auto;
                padding: 10px;
                background-color: #e0e0e0; /* Darker gray background for the chat box container */
            
                /* These lines center the box itself */
                margin: 0 auto 10px auto; 
                text-align: left;
            }

            /* Different backgrounds for the messages */
            .user-msg {
                background-color: white;
                padding: 5px;
                margin: 5px 0;
                border-radius: 4px;
            }

            .bot-msg {
                background-color: #ffcccc; /* Light red/pink background for the bot */
                padding: 5px;
                margin: 5px 0;
                border-radius: 4px;
            }
        </style>
    </head>
    <body>

        <h2>Gym Chatbot</h2>

        <!-- Chat display area -->
        <div id="chat-box">
            <div class="bot-msg"><strong>Bot:</strong> Hello! Ask me about price, location, or hours.</div>
        </div>

        <!-- Input and Button -->
        <input type="text" id="user-input" placeholder="Type here...">
        <button onclick="sendMsg()">Send</button>

        <script>
            function sendMsg() {
                // 1. Get the elements
                const chatBox = document.getElementById('chat-box');
                const inputField = document.getElementById('user-input');
            
                // 2. Get what the user typed and make it lowercase
                const message = inputField.value.trim().toLowerCase();
            
                // If the input is empty, do nothing
                if (message === "") return;

                // 3. Display the user's message wrapped in the user-msg class (White background)
                chatBox.innerHTML += "<div class='user-msg'><strong>You:</strong> " + inputField.value + "</div>";
            
                // Clear the input box for the next message
                inputField.value = "";

                // 4. Ultra-simple 'if/else' logic to check what the user asked
                let reply = "I'm sorry, I only know about price, location, or hours.";

                if (message.includes("price") || message.includes("cost") || message.includes("membership")) {
                    reply = "Memberships start at ₹1,499/month.";
                } 
                if (message.includes("location") || message.includes("where") || message.includes("address")) {
                    reply = "We are located in Koregaon Park, Pune.";
                } 
                if (message.includes("hour") || message.includes("time") || message.includes("open")) {
                    reply = "We are open 24/7!";
                }

                // 5. Display the bot's reply wrapped in the bot-msg class (Different background)
                chatBox.innerHTML += "<div class='bot-msg'><strong>Bot:</strong> " + reply + "</div>";
            
                // Auto-scroll to the bottom of the chat box
                chatBox.scrollTop = chatBox.scrollHeight;
            }
        </script>

    </body>
    </html>
    ```
