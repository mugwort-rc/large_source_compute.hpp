# large_source_compute.hpp

## Example

```cpp
// g++ example.cpp -std=c++17 -I /path/to/boost/>=1.54/ -lOpenCL

#include <boost/compute.hpp>

#include "large_source_compute.hpp"


BOOST_COMPUTE_FUNCTION(int, awesome_function, (double a, double b), {
    return static_cast<int>(a / b);
});


int main(int, char**) {
    boost::compute::device device = boost::compute::system::default_device();
    boost::compute::context context(device);
    boost::compute::command_queue queue(context, device);

    sep::compute::source::file<double> input1("very_very_large_input1.bin");
    sep::compute::source::file<double> input2("very_very_large_input2.bin");
    sep::compute::sink::file<int> output("output.bin");

    assert(input1.is_valid_type());
    assert(input2.is_valid_type());
    assert(input1.total() == input2.total());

    // memory limit
    const int memory_size = 1024 * 1024 * 1024;
    sep::compute::linear::manager manager(context, queue, memory_size);

    sep::compute::utility::measure_callback cb;
    manager.compute(input1, input2, output, awesome_function, &cb);

    cb.print_measure_report();
    // total: 182980msec; compute: 260.239msec transport: 182720msec.

    return 0;
}
```

