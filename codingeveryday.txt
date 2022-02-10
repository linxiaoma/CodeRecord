date：2022-02-10

/*
测试C++中vector容量的动态增长

*/
#include <iostream>
#include <vector>

int main(){
    func(nullptr);

    std::vector<int> vec;
    vec.reserve(100);

    std::cout << "The capacity of vec is " << vec.capacity() << std::endl;
    std::cout << "The size of vec is " << vec.size() << std::endl;
    int currentCapacity = vec.capacity();

    for(int i{0}; i < 1000; ++i){
        vec.push_back(i);
        if(currentCapacity != vec.capacity()){
            std::cout << "The new capacity of vec is " << vec.capacity() << std::endl;
            currentCapacity = vec.capacity();
        }
    }
}
