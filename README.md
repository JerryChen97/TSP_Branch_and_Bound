# TSP_Branch_and_Bound
Sovling TSP problem with the method of Branch and Bound<br>
##解决TSP问题的分支定界法类
###使用说明
  先通过BranchAndBound(int VertexNum, int EdgeNum, vector<Edge> initEdge)初始化该类<br>
  其中VertexNum为节点数目，EdgeNum为边的数目，initEdge为所有边组成的向量<br>
  注意边的编号从0开始，节点的编号也是从0开始<br>
  getHLoopLength()返回最短H回路的大小<br>
  FinalHLoop[0]--FinalHLoop[VertexNum]中存储着最短哈密顿回路中各边的编号<br>
  通过printFinalResult()即可打印结果<br>
