```rust
use std::mem;

pub struct List {
    head: Link,
}

struct Node {
    val: i32,
    next: Link,
}

enum Link {
    Empty,
    More(Box<Node>),
}

impl List {
    pub fn new() -> List {
        List { head: Link::Empty }
    }

    pub fn push(&mut self, val: i32) {
        let new_node = Box::new(Node {
            val,
            next: mem::replace(&mut self.head, Link::Empty),
        });
        self.head = Link::More(new_node);
    }

    pub fn pop(&mut self) -> Option<i32> {
        match mem::replace(&mut self.head, Link::Empty) {
            Link::Empty => None,
            Link::More(node) => {
                self.head = node.next;
                Some(node.val)
            }
        }
    }
}

fn main() {
    let mut list = List::new();
    list.push(5);
    let x = list.pop();
    let val = x.unwrap();
    let y = list.pop();
    let val2 = y.unwrap();
}

#[cfg(test)]
mod test {
    use crate::List;

    #[test]
    fn basics() {
        let mut list = List::new();

        assert_eq!(list.pop(), None);

        list.push(4);
        list.push(55);
        list.push(99999);

        assert_eq!(list.pop(), Some(99999));
        assert_eq!(list.pop(), Some(55));

        list.push(1);

        assert_eq!(list.pop(), Some(1));
        assert_eq!(list.pop(), Some(4));
        assert_eq!(list.pop(), None);
    }
}
```

VSCode sagt es verifiziert (es sollte eig erkennen das y.unwrap) einen panic auslösen wird, MIRAI erkennt das!

Achtung: Testcode (mod test ... ) löst bei Prusti einen unendlichen loop aus!