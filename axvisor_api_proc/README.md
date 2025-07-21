# axvisor-api-macros  

**Attribute Macros for Automated API Trait Generation and Cross-Module Binding**  

## Features  

Provides two attribute macros `#[api_mod]` and `#[api_mod_impl]` for **Hypervisor/OS development**:  

1. **Auto-generate Standardized API Traits** - Convert `extern fn` functions in modules into interface traits (e.g., `AxvisorMemoryApiTrait`)[6](@ref);  
2. **Cross-Module Implementation Binding** - Inject functions from implementation modules into trait dispatchers[6](@ref);  
3. **Zero Runtime Overhead** - Code generation at compile time with no runtime cost.  

## Installation  

Add to `Cargo.toml`:  

```toml
[dependencies]
axvisor-api-macros = "0.1.1"
```

## Usage Examples

1. **Define API Module**

   ```
   #[api_mod]
   /// Memory-related API
   pub mod some_demo {
       pub use memory_addr::PhysAddr;
   
       /// Some function
       extern fn some_func() -> PhysAddr;
       /// Another function
       extern fn another_func(addr: PhysAddr);
   }
   ```

   **Expanded:**

- Generates Trait AxvisorMemoryApiTrait
- Creates stub function gpa_alloc forwarding calls via call_interface!

2. **Implement API Module**

   ```
   #[api_mod_impl(some_demo)]
   mod some_impl {
       use memory_addr::{PhysAddr, pa};
   
       extern fn some_func() -> PhysAddr {
           return pa!(0x42);
       }
   
       extern fn another_func(addr: PhysAddr) {
           println!("Wow, the answer is {:?}", addr);
       }
   }
   ```

   **Expanded:**

- Creates struct __Impl
- Binds functions to target trait via impl_interface

## Use Cases

- Hypervisor Guest Operations (e.g., memory management, interrupt handling)
- Microkernel Service Interfaces (filesystem, device drivers)
- Embedded HAL Layer device abstractions