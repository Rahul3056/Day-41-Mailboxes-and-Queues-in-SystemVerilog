

### **Day 41: Mailboxes and Queues in SystemVerilog**

---

### **1. Introduction**

In SystemVerilog, **mailboxes** and **queues** are used for **communication and synchronization** between processes (especially in testbenches).

- **Mailboxes**: Communication mechanism — think of them like a postbox to send and receive messages safely between processes.
- **Queues**: Data structure — dynamic arrays that grow and shrink automatically, useful to store elements in an ordered list.

---

### **2. Mailboxes**

#### **What is a Mailbox?**
- Mailboxes allow **safe transfer of data** between different processes.
- They are **thread-safe** — no need to worry about race conditions.
- Typically used in **testbenches** to communicate between **stimulus** and **driver** threads.

---

#### **Types of Mailboxes**

- **Bounded Mailbox**: Has a limited size (optional).
- **Unbounded Mailbox**: No size limit.

---

#### **Declaring and Creating Mailbox**

```systemverilog
mailbox mbox;            // Declaration
mbox = new();            // Creation (unbounded)

mailbox #(int) mbox2;     // Strongly typed mailbox
mbox2 = new(10);          // Bounded to 10 items
```

---

#### **Basic Mailbox Methods**

| Method         | Purpose                          |
|----------------|-----------------------------------|
| `put(item)`    | Insert an item into the mailbox   |
| `get(item)`    | Retrieve (remove) an item         |
| `peek(item)`   | Read an item without removing     |
| `try_get(item)`| Non-blocking get                  |

---

#### **Example: Mailbox Communication**

```systemverilog
module mailbox_example;

    mailbox mbox; // declare

    initial begin
        mbox = new(); // create

        fork
            // Producer
            begin
                #5;
                mbox.put(42);
                $display("Producer: sent 42");
            end

            // Consumer
            begin
                int data;
                mbox.get(data);
                $display("Consumer: received %0d", data);
            end
        join
    end

endmodule
```

**Output:**
```
Producer: sent 42
Consumer: received 42
```

---

### **3. Queues**

#### **What is a Queue?**
- A **dynamic array** that can automatically grow and shrink.
- Ordered collection: first element at index 0.

---

#### **Declaring Queues**

```systemverilog
data_type queue_name[$];
```

Example:

```systemverilog
int my_queue[$];    // Queue of integers
```

---

#### **Basic Queue Operations**

| Operation               | Example                          |
|--------------------------|----------------------------------|
| Insert at end            | `my_queue.push_back(value);`     |
| Insert at beginning      | `my_queue.push_front(value);`    |
| Delete from end          | `my_queue.pop_back();`           |
| Delete from beginning    | `my_queue.pop_front();`          |
| Queue size               | `my_queue.size();`               |

---

#### **Example: Queue Usage**

```systemverilog
module queue_example;

    int q[$]; // declare a queue

    initial begin
        q.push_back(10);
        q.push_back(20);
        q.push_back(30);

        $display("Queue contents: %p", q);

        q.pop_front(); // removes 10
        $display("Queue after pop: %p", q);
    end

endmodule
```

**Output:**
```
Queue contents: '{10, 20, 30}
Queue after pop: '{20, 30}
```

---

### **4. Mailbox vs Queue**

| Feature       | Mailbox                         | Queue                        |
|---------------|----------------------------------|-------------------------------|
| Purpose       | Process communication           | Data storage/handling         |
| Blocking?     | Yes (`put`, `get`) can block     | No blocking operations        |
| Size          | Bounded or unbounded             | Dynamic growth/shrink         |
| Synchronization| Built-in                        | Manual (if needed)            |

---

### **5. Summary**

- **Mailbox** is mainly for **process synchronization** (producer-consumer model).
- **Queue** is for **dynamic storage** and **ordering** of elements.
- Both are **critical for building testbenches** in SystemVerilog, especially in UVM environments.

---

