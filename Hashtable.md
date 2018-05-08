# Hashtable implementation principle
### Introduction
Like HashMap, Hashtable is also a hash table, and its stored content is a key-value pair.

Hashtable is defined in Java as:
```java
public class Hashtable<K,V>  
    extends Dictionary<K,V>  
    implements Map<K,V>, Cloneable, java.io.Serializable{}
```
From the source code, we can see that Hashtable inherits from the Dictionary class and implements the Map, Cloneable, and java.io.Serializable interfaces. Where the `Dictionary` class is an abstract superclass of any class (such as a `Hashtable`) that can map keys to corresponding values, each key and value is an object (the source code annotation is: The Dictionary class is the abstract parent of any class, such as `Hashtable` , which maps keys to values. Every key and every value is an object.). But at this point I began to doubt a bit, because I looked at the source code of HashMap and TreeMap, none of them inherited from this class. However, when I see the explanation in the comments, I understand that the source comment of the Dictionary is: NOTE: This class is obsolete. New implementations should implement the Map interface, rather than extend this class. This indicates that the class Dictionary is obsolete. The new implementation class should implement the Map interface.  

### Source code reading
`Hashtable` is a hash table implemented by the "zip method". It includes several important member variables: table, count, threshold, loadFactor, modCount.

* `Table` is an Entry[] array type, and Entry (explained in HashMap) is actually a one-way linked list. The "key-value key-value pairs" of the hash table are stored in the Entry array.
* `Count` is the size of the `Hashtable`, which is the number of key-value pairs saved by the `Hashtable`.
* `Threshold` is the `Hashtable` threshold used to determine if the `Hashtable` capacity needs to be adjusted. The value of threshold = "capacity * load factor".
* `modCount` is used to implement the fail-fast mechanism.    

```java
/**
     * The hash table data.
     */
    private transient Entry<K,V>[] table;

    /**
     * The total number of entries in the hash table.
     */
    private transient int count;

    /**
     * The table is rehashed when its size exceeds this threshold.  (The
     * value of this field is (int)(capacity * loadFactor).)
     *
     * @serial
     */
    private int threshold;

    /**
     * The load factor for the hashtable.
     *
     * @serial
     */
    private float loadFactor;

    /**
     * The number of times this Hashtable has been structurally modified
     * Structural modifications are those that change the number of entries in
     * the Hashtable or otherwise modify its internal structure (e.g.,
     * rehash).  This field is used to make iterators on Collection-views of
     * the Hashtable fail-fast.  (See ConcurrentModificationException).
     */
    private transient int modCount = 0;
```
### Constructor
4 kinds of constructor are provided by `Hashtable`

* `Public Hashtable(int initialCapacity, float loadFactor)`: Constructs a new empty hash table with the specified initial capacity and the specified load factor. useAltHashing is boolean, and if it is true, another hashed string key is executed to reduce the occurrence of hash conflicts due to weak hash calculations.
* `Public Hashtable(int initialCapacity)`: Constructs a new, empty hash table with the specified initial capacity and the default load factor (0.75).
* `Public Hashtable()`: The default constructor with a capacity of 11 and a load factor of 0.75.
* `Public Hashtable(Map<? extends K, ? extends V> t)`: Constructs a new hash table with the same mapping as the given Map.

```java
/**
     * Constructs a new, empty hashtable with the specified initial
     * capacity and the specified load factor.
     *
     * @param      initialCapacity   the initial capacity of the hashtable.
     * @param      loadFactor        the load factor of the hashtable.
     * @exception  IllegalArgumentException  if the initial capacity is less
     *             than zero, or if the load factor is nonpositive.
     */
    public Hashtable(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal Load: "+loadFactor);

        if (initialCapacity==0)
            initialCapacity = 1;
        this.loadFactor = loadFactor;
        table = new Entry[initialCapacity];
        threshold = (int)Math.min(initialCapacity * loadFactor, MAX_ARRAY_SIZE + 1);
        useAltHashing = sun.misc.VM.isBooted() &&
                (initialCapacity >= Holder.ALTERNATIVE_HASHING_THRESHOLD);
    }

    /**
     * Constructs a new, empty hashtable with the specified initial capacity
     * and default load factor (0.75).
     *
     * @param     initialCapacity   the initial capacity of the hashtable.
     * @exception IllegalArgumentException if the initial capacity is less
     *              than zero.
     */
    public Hashtable(int initialCapacity) {
        this(initialCapacity, 0.75f);
    }

    /**
     * Constructs a new, empty hashtable with a default initial capacity (11)
     * and load factor (0.75).
     */
    public Hashtable() {
        this(11, 0.75f);
    }

    /**
     * Constructs a new hashtable with the same mappings as the given
     * Map.  The hashtable is created with an initial capacity sufficient to
     * hold the mappings in the given Map and a default load factor (0.75).
     *
     * @param t the map whose mappings are to be placed in this map.
     * @throws NullPointerException if the specified map is null.
     * @since   1.2
     */
    public Hashtable(Map<? extends K, ? extends V> t) {
        this(Math.max(2*t.size(), 11), 0.75f);
        putAll(t);
    }
```
### put method
The entire process of the put method is:

1. Determine if value is empty or empty and throw an exception.
2. Calculate the hash value of key and get the position of key in the table array index according to the hash value. If the table[index] element is not empty, then iterate. If the same key is encountered, replace it directly and return the old value.
3. Otherwise, we can insert it into table[index].

```java
public synchronized V put(K key, V value) {
        // Make sure the value is not null确保value不为null
        if (value == null) {
            throw new NullPointerException();
        }

        // Makes sure the key is not already in the hashtable.
        Entry tab[] = table;
        int hash = hash(key);
        int index = (hash & 0x7FFFFFFF) % tab.length;
        for (Entry<K,V> e = tab[index] ; e != null ; e = e.next) {
            if ((e.hash == hash) && e.key.equals(key)) {
                V old = e.value;
                e.value = value;
                return old;
            }
        }

        modCount++;
        if (count >= threshold) {
            // Rehash the table if the threshold is exceeded
            rehash();

            tab = table;
            hash = hash(key);
            index = (hash & 0x7FFFFFFF) % tab.length;
        }

        // Creates the new entry.
        //insert the key and return null
        Entry<K,V> e = tab[index];
        tab[index] = new Entry<>(hash, key, value, e);
        count++;
        return null;
    }
```       
### get method
Compared to the put method, the get method is much simpler. The process is to first obtain the hash value of key through the hash() method, and then get the index index according to the hash value (the algorithm used in the above two steps is the same as the put method). Then iterate over the linked list and return the corresponding value of the matching key; return null if it is not found.

```java
Entry tab[] = table;
int hash = hash(key);
int index = (hash & 0x7FFFFFFF) % tab.length;
for (Entry<K,V> e = tab[index] ; e != null ; e = e.next) {
    if ((e.hash == hash) && e.key.equals(key)) {
        return e.value;
    }
}
return null;
}
```
