This program contents
- task.txt
- completed.txt
- README.md
- task(obj file)

Demo of program: https://youtu.be/CNCsFNLYb8Q

This is a cli tool to manage your important tasks first by assigning higher value, so it can pop on the top of the list. 

Usage 
./task 
./task help
./task del [index]
./task ls
./task add [priority number] ["task"]
./task report


All above program method can be call through command line...

Thank you!

---
---

/**
 * @file task.cpp
 * @author Shivesh Kumar Singh
 * @version 1
 * @date 2021-12-10
 * 
 * @copyright Copyright (c) 2021
 * 
 */

#include <stdlib.h>

#include <iostream> 
#include <fstream> 
#include <queue>
#include <utility>

#include <vector> 
#include <filesystem>
using namespace std; 


/**help method
 * @brief This method is used to show all usage instruction for the user. 
 *
 */

void help()
{

cout<<"Usage :- \n" 
"$ ./task add 2 hello world    # Add a new item with priority 2 and text \"hello world\" to the list \n"
"$ ./task ls                   # Show incomplete priority list items sorted by priority in ascending order \n"
"$ ./task del INDEX            # Delete the incomplete item with the given index \n"
"$ ./task done INDEX           # Mark the incomplete item with the given index as complete \n"
"$ ./task help                 # Show usage \n"
"$ ./task report               # Statistics; \n";
}

/**ls method
 * @brief This method is used to show all task present in task.txt * 
 * @param queue which contains pairs of value (priority, string)
 */

void ls( priority_queue<pair<int, string> > queue)
{
  
    while(!queue.empty())
    {
       pair<int, string> two_items = queue.top(); 
        
        
       cout<<two_items.first<<" "<<two_items.second<<endl; 
       queue.pop(); 
    }
 
    
}
    
/**add method
 * @brief This method is used to add task to task.txt file 
 * 
 * @param queue which contains pairs of value (priority, string) 
 * @param argv command line argument is used to add a pair in priority queue(int, string)
 */

void add(priority_queue<pair<int, string> > &queue, char* argv[])
{

int count = 0 ; 

queue.push(make_pair(atoi(argv[2]), string(argv[3])));

ofstream file; // output stream

file.open("task.txt", ios::out); 

if(file.is_open())
while(!queue.empty())
{
count++; 

pair<int, string> two_items = queue.top();

if (count == 1)
{file<<two_items.first<<" "<<two_items.second; }
else
{file<<"\n"<<two_items.first<<" "<<two_items.second; }
queue.pop(); 

}

cout<<"Added task:"<<"\""<<argv[3]<<"\""<<" with priority "<<argv[2]<<"\n";

file.close(); 


}

/**del method
 * @brief This method is used to del the task from task.txt
 * 
 * @param queue which contains pairs of value (priority, string)
 * @param argv to hold array of strings via command line argument.
 */

void del(priority_queue<pair<int, string> > &queue, char* argv[])
{
int count = atoi(argv[2]);

    // check if given  index exist or not ?
if( atoi(argv[2]) > 0 && atoi(argv[2]) <= queue.size())
{   
    
    vector<pair<int, string> > temp_vec; 
    
    while(!queue.empty())
    {
    
    pair<int, string> two_items = queue.top(); 
    
    temp_vec.push_back(make_pair(two_items.first, two_items.second)); 
    
    queue.pop(); 
    
    
    }
    
    
    temp_vec.erase(temp_vec.begin() + atoi(argv[2]) );
    
    for(vector<pair<int, string> >:: iterator it = temp_vec.begin(); it != temp_vec.end(); it++)
    {
        queue.push(make_pair(it->first, it->second));
    
    }
    //-------
    ofstream file; // output stream

file.open("task.txt", ios::out); 

if(file.is_open())
while(!queue.empty())
{

pair<int, string> two_items = queue.top();

if(count == 0)
{
file<<two_items.first<<" "<<two_items.second;
}
else
{

file<<"\n"<<two_items.first<<" "<<two_items.second;
}


queue.pop(); 
}

file.close(); 

    
    cout<<"Deleted item with index "<<atoi(argv[2])<<"\n";
    
}
    

else 
{

    cout<<"Error: item with index "<<atoi(argv[2])<<" does not exist. Nothing deleted."<<"\n";

}
} 

/**done method
 * @brief This method is used save completed task in the complete.txt
 * 
 * @param queue which contains pairs of value (priority, string)
 * @param argv command line argument: which contains array of strings
 */
void done(priority_queue<pair<int, string> >&queue, char* argv[])
{
  int count = 0;
  vector<pair<int, string> > vec; 
  
  string completed_task; 
  
if(atoi(argv[2])> 0 && atoi(argv[2]) <= queue.size())
{    
  while(!queue.empty())
  {
    count++; 
    
   if(count == atoi(argv[2]))   
  {     
        pair<int,string>two_items = queue.top();
        
        completed_task = two_items.second; 
            
        queue.pop();
        
        
  } 
    else
    {
        pair<int, string> two_items = queue.top(); 
         
        vec.push_back(make_pair(two_items.first, two_items.second));
            
        queue.pop();
    }   
    
  
  
  }
  
  // Wrting the completed task in completed.txt
  ofstream file("completed.txt", ios::app);
  
  if(file.is_open())
    file<<'\n'<<completed_task;     
  else
    cout<<"file is not open "; 
  
  file.close(); 
  
  // Putting the elements back to priority queue 
  
  for (vector<pair<int, string> >:: iterator it = vec.begin(); it != vec.end(); it++)
  {
  
  queue.push(make_pair(it->first, it->second)); 
  
  }
  
  
  // Updating the task.txt
    
    int count = 0; 
    ofstream myfile("task.txt"); 
    
    if(myfile.is_open())
    {

    while(!queue.empty())
    {
    pair<int,string> two_items = queue.top(); 
    if(count == 0)    
    {
    myfile<<two_items.first<<" "<<two_items.second; 
    queue.pop();
    }
    else
    {myfile<<'\n'<<two_items.first<<" "<<two_items.second;
     queue.pop(); 
    }
    
    count++; }
    
    }
    else 
    cout<<"file is not open "; 
    
    myfile.close(); 
  
}
  
else
    cout<<"Error: no incomplete item with index "<<argv[2]<<" exists.";
    
  

}
/**pending method
 * @brief This method is used to list all the pending task from task.txt called report is called through command line argument
 * 
 * @param queue which contains pairs of value (priority, string)
 */
void pending(priority_queue<pair<int, string> > &queue)
{
int count = 0; 

    ifstream file("task.txt");
    cout<<"Pending : "<<queue.size()<<'\n';
        
    if(file.peek() == std::ifstream::traits_type::eof())
    {
    cout<<"No pending task"; 
    
    }
    
    
    while(!queue.empty())
    {
    pair<int, string> top = queue.top(); 
    count++; 
    if( count == 1)
    {
    cout<<count<<". this is a pending task ["<<top.first <<"]\n"; 
   
    }
    else
    {
    cout<<count<<". this is a pending task with priority ["<<top.first<<"]\n";
    }
    queue.pop(); 
    }
    
    
    
    


}

/**complete method 
 * @brief This method is used to retrieve the completed task from completed.txt. This method is called when report is callend via command line argument.
 * 
 */
void completed()
{



priority_queue<string > queue ; 

ifstream file; 
    file.open("completed.txt", ios::in);
    if(file.peek() == std::ifstream::traits_type::eof() )
    {
        
     cout<<"no task is completed";   
     return;    
    }
    
    
    else if(file.is_open()){
     
   
    string task; 
    
    while(!file.eof())
    {
    
    getline(file, task);
        
    queue.push(task);
    
 
    }
    
    cout<<"Completed : "<<queue.size()<<'\n';
    
    
 for(int i = 1; i<=queue.size(); i++)
    {
    if( i == 1)
    {
    cout<<i<<". completed task \n"; 
    }
    else if(i == 2)
    {
    cout<<i<<". another completed task \n";
    }
    else
    {
    cout<<i<<". yet another completed task \n";
    }

    }}

    
    else
    {
    
    cout<<"File did not open"; 
    
    }


}


int main(int argc, char* argv[]){




  
  
  // Data structure used priority queue
  priority_queue<pair<int, string> > queue; 
    
    ifstream file; 
    file.open("./task.txt", ios::in);
    
    int priority; 
    string task; 
    if(file.peek() == std::ifstream::traits_type::eof())
    {
        // doesn't insert item when queue is empty...
    }
    
    else if(file.is_open())
    {
    
    while(!file.eof())
    {
        
        file>>priority; 
        getline(file, task); 
            
        queue.push(make_pair(priority, task));    
    }
    file.close(); 
    }
    else
    {
    cout<<"file is not opened"; 
    }
  
  
  


// There is always count argc is one because array first element contain the filename
if((argc == 1) ||( argc == 2 && string(argv[1]) == "help") )
{

    help();
    
}
else if (string(argv[1])== "ls")
{

    ls(queue);    
    
}

else if(string(argv[1]) == "add")
{

    add(queue,argv);

}

else if(string(argv[1]) == "del")
{
    del(queue, argv);
}
else if(string(argv[1]) == "done")
{
    if(argc ==3)
    done(queue, argv);
    else
    cout<<"please enter in given format "<<"[./task] [done] [task number]";
}
else if(string(argv[1]) =="report")
{

//pending task(task.txt)
pending(queue);

//completed task(task.txt)
completed(); 


}



return 0;     
}
