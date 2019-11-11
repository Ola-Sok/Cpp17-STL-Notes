# Structured bindings for unpacking bundled return values
Can unpack:
- std::tuple
- std::pair
- custom structures
- std::map
- fundamental STL data structures

Structured bindings combine automatic type deduction and syntactic sugar, which serve for the purpose of extracting and assigning needed values to individual variables. The number of variables to bind to must agree with the number of returned values.

### std::tuple
```C++
//function prototype:
std::tuple<std::string, std::chrono::system_clock::time point, unsigned>
stock_info(const std::string &name);

//assigning using structured binding
const auto [name, valid_time, price = stock_info("INTC");
```
### std::pair
```C++
//function prototype:
std::pair<int, int> divide_remainder (int dividend, int divisor);

//accessing the individual values of the result of a function with STRUCTURED BINDING
auto [fraction, remainder] = divide_remainder(16, 3);
std::cout << "16 / 3 is "
	<< fraction << " with a remainder of "
	<< remainder << '/n';
		  
// old way of accessing values of an std::pair
const auto result (divide_remainder(16, 3));
std::cout << "16 / 3 is "
	<< result.first << " with a remainder of "
	<< result.second << '/n';
```
### custom structures
```C++
//Practice using structured bindings on custom structures.
#include <iostream>
#include <vector>
//Define & initialize data structures
struct employee {
	unsigned int id;
	std::string name;
	std::string role;
	unsigned salary;
} e1, e2, e3;

std::vector<employee> employees {
	e1, e2, e3/* Initialized from somewhere */
};
std::vector<std::string> init_names_of_employees (const std::vector<employee> employees) {
	std::vector<std::string> names_of_employees;
	for (const auto &[id, name, role, salary] : employees) {
	// If we don't use all of the binded variables, compiler will optimize it out and will not bind values to unused variables.
		names_of_employees.push_back(name);
	}
	return names_of_employees;
}
const std::vector<std::string> names_of_employees = init_names_of_employees(::employees);

void print_names (std::vector<std::string> names_of_employees) {
    for (const auto &name : names_of_employees) {
        std::cout << name << std::endl;
    }
}
//
//
//
//
int main() {
	//notice, we access global scope variable specifically
	init_names_of_employees (::employees);
    print_names(::names_of_employees);
}
	
```
### std::map
```C++
#include <map>

std::map<std::string, size_t> animal_population {
	{"humans", 700000000000},
	{"chickens", 5802482509235849},
	{"camels", 97675678},
	"sheep", 742035748955},
	/* ... */
};
for (const auto &[species, count] : animal_population) {
	std::cout << "There are " << count << " " << species
		<< "on this planet.\n";
```
This particular example works because when we iterate over an std::map container, we get the std::pair<const key_type, value type> nodes on every iteration.
Exactly those nodes are unpacked using the structured bindings feature (key_type is the 'species' string and value_type is the population count size_t) in order to access them individually in the loop body.
