#include "stdafx.h"
#include <vector>
#include <algorithm>
#include <iostream>
using namespace std;
//------------描述边的状态------------------------------
enum State
{
	INSTACK,	//在栈中 
	UNVISITED,	//尚未被访问
	VISITED		//已被访问
};
//-------------边类--------------------------------------
class Edge{
public:
	int weight;		//各边的权重
	State edgeState;	//边的状态
	int lPoint, rPoint;		//各边的左右端点
	Edge(int weight, int lPoint, int rPoint){
		this->weight = weight; 
		this->lPoint = lPoint;
		this->rPoint = rPoint;
		this->edgeState = UNVISITED;	//初始时所有边都未被访问
	}
	Edge(){}
};

//-------------端点类------------------------------------------
class Node{
public:
	int prev;	//端点的前驱端点的地址(编号)
	int next;	//端点的后继端点的地址(编号)
	Node(){prev = -1; next = -1;}	//初始化为-1,表示一开始不存在相邻端点
};

//-----------------解决TSP问题的分支定界法类----------------------
//-----------------使用说明----------------------------------------------------------
//先通过BranchAndBound(int VertexNum, int EdgeNum, vector<Edge> initEdge)初始化该类
//其中VertexNum为节点数目，EdgeNum为边的数目，initEdge为所有边组成的向量
//注意边的编号从0开始，节点的编号也是从0开始
//getHLoopLength()返回最短H回路的大小
//FinalHLoop[0]--FinalHLoop[VertexNum]中存储着最短哈密顿回路中各边的编号
//通过printFinalResult()即可打印结果
//-----------------------------------------------------------------------------------
class BranchAndBound{
public:
	BranchAndBound(int VertexNum, int EdgeNum, vector<Edge> initEdge);	//构造一个分支定界法类
	bool DFS();	//用DFS方法进行求解
	void printFinalResult();	//打印最后的结果
	int getHLoopLength(){return bound;}	//返回最短H回路的大小
	vector<int>* getHLoop(){return &FinalHLoop;}	//返回最短H回路上各边的编号

private:
	int VertexNum;	//节点数量
	int EdgeNum;	//边数
	vector<Edge> edgeVector;	//所有边的信息
	int bound; //界
	int getLoopLength();	//得到当前回路的带权长度
	bool isHLoop(); //判断是否为哈密顿回路
	int* Loop;	//当前回路(实际存储的是边的编号)
	vector<int> FinalHLoop;	//存储最短的哈密顿回路信息(实际存储的是边的编号)
	bool Pop();	//退栈
	bool Push();//入栈
	void printStatus();	//打印当前各边的状态
};


//------------------打印最后的结果----------------------------
void BranchAndBound::printFinalResult(){
	cout << "-------The shortest H circuit-------" << endl;
	cout << "Length: " << bound << endl;	//输出最短H回路的长度
	cout << "Path: " ;	//输出最短H

	//--------------记录每个端点的前驱和后继-------------------
	Node* vertexNode = new Node[VertexNum];
	for(int i = 0; i < VertexNum; i++)
	{
		int lPoint = edgeVector[FinalHLoop[i]].lPoint;	//第i条边的左端点
		int rPoint = edgeVector[FinalHLoop[i]].rPoint;	//第i条边的右端点
		//--------------记录每个端点的前驱和后继-------------------
		if(vertexNode[lPoint].prev == -1)	//若lPoint不存在前驱
			vertexNode[lPoint].prev = rPoint;	//则让rPoint成为lPoint的前驱
		else
			vertexNode[lPoint].next = rPoint;	//否则让rPoint成为lPoint的后继
		if(vertexNode[rPoint].prev == -1)	//若rPoint不存在前驱
			vertexNode[rPoint].prev = lPoint;	//则让lPoint成为rlPoint的前驱
		else
			vertexNode[rPoint].next = lPoint;	//否则让lPoint成为rPoint的后继
	}

	int j = 0; //从端点0出发
	int oldI = -1; //oldI记录上一次访问的端点
	int count = 0;	//访问过的端点数目，一开始为0
	cout << j << " -> " ;
	while(true){	//沿着端点的相邻点向前走
		if(vertexNode[j].prev != oldI){	//若尚未经过端点i的前驱
			oldI = j;	//将这次访问的端点记录在oldI中
			j = vertexNode[j].prev;		//则下一个循环经过端点j的前驱
		}
		else{	//否则
			oldI = j;	//将这次访问的端点记录在oldI中
			j = vertexNode[j].next;		//下一个循环经过端点j的后继
		}
		if(j > 0)	//如果还没有回到出发点
			cout << j << " -> " ;	//则继续访问
		else if(j == 0){	//如果回到了出发点
			cout << j << endl;
			cout << "----------------------------------" << endl;
			break;	//则退出
		}
	}
}

//-------------------打印各边的状态信息(作为调试使用)-----------------------
void BranchAndBound::printStatus(){
	char* statusChar[] = {"INSTACK", "UNVISITED", "VISITED"};
	for(int i = 0; i < EdgeNum; i++){
		if(edgeVector[i].edgeState == INSTACK) {
			//输出栈中的边的左右端点
			cout << "(" << edgeVector[i].lPoint << " , " << edgeVector[i].rPoint  << ")  "; 
			//输出所有边的状态
			//cout << "(" << edgeVector[i].lPoint << " , " << edgeVector[i].rPoint << ") Status: " << statusChar[edgeVector[i].edgeState] << endl;
		}
	}
	cout << "Length: " << getLoopLength();	//输出当前回路的长度
	cout << endl;
	cout << "---------------------------------------------" << endl;
}

//-----------入栈(依次删除当前si中的最长边，加入后面第一条待选边，继续深探)---------------
bool BranchAndBound::Push(){	
	int& last = Loop[VertexNum-1];	//若哈密顿回路中最大的元素
	if(last >= EdgeNum)	//超出了边的范围,
		return false;	//则返回false
	edgeVector[last].edgeState = VISITED;	//将当前最大的元素置为已访问(即删除最长边)
	if(last == EdgeNum - 1)	//如果当前最大的元素同时也是边中编号最大的
		return false;	//则不能再入栈了	
	else
		edgeVector[++last].edgeState = INSTACK; //否则入栈下一个元素(加入后面第一条待选边)
	return true;
}

//------------------------------退栈------------------------------------------------------
bool BranchAndBound::Pop(){	
	int last = Loop[VertexNum-1];	//哈密顿回路中最大的元素
	int countI = 0;		//countI用来记录退栈过程中被弹出的的元素个数
	int i,j;
	//----从回路中最大元素出发向小端前进，将INSTACK全部改为UNVISITED，直到遇到首个VISITED元素------
	for(i = last; edgeVector[i].edgeState == INSTACK; i--)	
	{
		countI++;	//退栈过程中被弹出的的元素个数加一
		edgeVector[i].edgeState = UNVISITED;
		Loop[i] = -1;
	}
	//----从最大的VISITED元素出发向小端前进，将VISITED全部改为UNVISITED，直到遇到首个INSTACK元素------
	for(j = i; edgeVector[j].edgeState == VISITED; j--){
		edgeVector[j].edgeState = UNVISITED;	
		if(j == 0) return false; //如果已经到达序号最小的边，说明已经栈空，返回
	}
	//-------将首个STACK元素改为VISITED，退栈被弹出的元素数加一-----------------------------------
	edgeVector[j].edgeState = VISITED; countI++; 
	//----从上述元素出发向大端前进，沿途入栈countI个元素
	for(int k = j + 1, l = 0; l < countI; k++, l++){
		edgeVector[k].edgeState = INSTACK;	//入栈沿途的元素
		Loop[VertexNum-countI+l] = k;
	}
	return true;
}

bool BranchAndBound::DFS(){
	while(true){
		while(true){
			int length = getLoopLength();	//计算H回路的路径长度
			if(length >= bound)	//如果当前的路径长度大于上界
				break;	//退出循环,进入退栈
			else if(isHLoop()){	//如果构成了H回路
				bound = length;	//改变界
				FinalHLoop.assign(Loop, Loop+VertexNum);	//将当前的H回路存入FianlHLoop
				break;	//退出循环,进入退栈
			}
			if(!Push())	//入栈，删除当前si的最长边，加入后面一条待选边，进行深探
				break;	//如果入栈失败(说明已经到达编号最大的边)，则退出循环，进入退栈
		}
		if(!Pop())	//退栈，如果栈空
			break; //结束大循环
	}
	return true;
}

bool sortByWeight(Edge e1, Edge e2){ //按照边的权重升序排列
	return (e1.weight < e2.weight);
}


BranchAndBound::BranchAndBound(int VertexNum, int EdgeNum, vector<Edge> initEdge){
	//------------初始化所有的边---------------------------------
	this->VertexNum = VertexNum;
	this->EdgeNum = EdgeNum;
	edgeVector.assign(initEdge.begin(), initEdge.end());

	bound = INT_MAX; //初始时界为无穷大

	//--------------将其按照权重weight升序排列-------------------
	sort(edgeVector.begin(), edgeVector.end(), sortByWeight);

	//--------------初始化H回路的栈------------------------------
	Loop = new int[VertexNum];
	for(int i = 0; i < VertexNum; i++){
		Loop[i] = i;	//H回路栈存放的是边的编号(地址)
		edgeVector[i].edgeState = INSTACK;	//将前VertexNum条边压入栈中
	}

	if(isHLoop()){	//如果前vertexNum个端点即构成H回路
		bound = getLoopLength();	//则求解完毕
	}
	else	//否则
		DFS();	//用DFS方法进行深探
	printFinalResult();//打印结果
}


//--------------------判断是否为哈密顿回路---------------------
bool BranchAndBound::isHLoop(){
	int* degree = new int[VertexNum];//标记VertexNum个节点的度
	for(int i = 0; i < VertexNum; i++){
		degree[i] = 0;
	}
	//---------------先判断回路中各个点的度是否均为2-----------
	for(int i = 0; i < VertexNum; i++){
		degree[edgeVector[Loop[i]].lPoint]++;
		degree[edgeVector[Loop[i]].rPoint]++;
	}
	for(int i = 0; i < VertexNum; i++){
		if(degree[i] != 2)
			return false;
	}

	//----------------再判断是否能构成一个(而不是多个)回路-------
	Node* vertexNode = new Node[VertexNum];
	for(int i = 0; i < VertexNum; i++)
	{
		int lPoint = edgeVector[Loop[i]].lPoint;	//第i条边的左端点
		int rPoint = edgeVector[Loop[i]].rPoint;	//第i条边的右端点
		//--------------记录每个端点的前驱和后继-------------------
		if(vertexNode[lPoint].prev == -1)	//若lPoint不存在前驱
			vertexNode[lPoint].prev = rPoint;	//则让rPoint成为lPoint的前驱
		else
			vertexNode[lPoint].next = rPoint;	//否则让rPoint成为lPoint的后继
		if(vertexNode[rPoint].prev == -1)	//若rPoint不存在前驱
			vertexNode[rPoint].prev = lPoint;	//则让lPoint成为rlPoint的前驱
		else
			vertexNode[rPoint].next = lPoint;	//否则让lPoint成为rPoint的后继
	}

	int j = 0; //从端点0出发
	int oldI = -1;
	int count = 0;	//访问过的端点数目，一开始为0
	while(true){	//沿着端点的相邻点向前走
		if(vertexNode[j].prev != oldI){	//若尚未经过端点i的前驱
			oldI = j;	//将这次访问的端点记录在oldI中
			j = vertexNode[j].prev;		//则下一个循环经过端点j的前驱
		}
		else{	//否则
			oldI = j;	//将这次访问的端点记录在oldI中
			j = vertexNode[j].next;		//下一个循环经过端点j的后继
		}
		count++;	//访问过的端点数加1
		if((j == 0) && (count < VertexNum))	//如果在没有经过所有端点的情况下就回到了出发点
			return false;	//说明不是哈密顿回路
		if(count == VertexNum)
			return true;
	}
}

//-----------------得到哈密顿回路的带权长度-------------------
int BranchAndBound::getLoopLength(){
	int length = 0;
	for(int i = 0; i < VertexNum; i++)
		length += edgeVector[Loop[i]].weight;
	return length;
}

int main(){
	//-----------计算样例------------------------------------
	int vertexNum = 5;
	int edgeNum = 10;
	Edge* edgeArray = new Edge[edgeNum];
	edgeArray[0] = Edge(3, 4, 2);
	edgeArray[1] = Edge(4, 3, 1);
	edgeArray[2] = Edge(4, 0, 4);
	edgeArray[3] = Edge(9, 0, 3);
	edgeArray[4] = Edge(10, 0, 1);
	edgeArray[5] = Edge(10, 0, 2);
	edgeArray[6] = Edge(11, 2, 3);
	edgeArray[7] = Edge(13, 1, 2);
	edgeArray[8] = Edge(16, 3, 4);
	edgeArray[9] = Edge(20, 1, 4);
	vector<Edge> edgeVector(edgeArray, edgeArray+edgeNum);
	BranchAndBound bab(vertexNum, edgeNum, edgeVector);
	return 0;
};
