#include <stdio.h>
#include <stdlib.h>

// 1. Song Queueing (Linear Queue) - CO1
#define MAX 100
int upNextQueue[MAX];
int front = -1, rear = -1;

void enqueue_song(int song_id) {
    if (rear == MAX - 1) {
        printf("Song Queue is full!\n");
    } else {
        if (front == -1) front = 0;
        rear++;
        upNextQueue[rear] = song_id;
        printf("Song ID %d added to Up Next.\n", song_id);
    }
}

int dequeue_song() {
    if (front == -1 || front > rear) {
        printf("No songs in the queue.\n");
        return -1;
    } else {
        int played_song = upNextQueue[front];
        front++;
        return played_song;
    }
}

// 2. Recently Played (Circular Queue) - CO1
#define HISTORY_SIZE 5
int recentQueue[HISTORY_SIZE];
int c_front = -1, c_rear = -1;

void add_recently_played(int song_id) {
    if ((c_front == 0 && c_rear == HISTORY_SIZE - 1) || (c_rear == (c_front - 1) % (HISTORY_SIZE - 1))) {
        c_front = (c_front + 1) % HISTORY_SIZE;
    } else if (c_front == -1) {
        c_front = 0;
    }
    c_rear = (c_rear + 1) % HISTORY_SIZE;
    recentQueue[c_rear] = song_id;
    printf("Song ID %d added to Recently Played.\n", song_id);
}

// 3. Playlist Add/Remove (Doubly Linked List) - CO2
struct PlaylistNode {
    int song_id;
    struct PlaylistNode* prev;
    struct PlaylistNode* next;
};
struct PlaylistNode* head = NULL;

void insert_playlist(int song_id) {
    struct PlaylistNode* newNode = (struct PlaylistNode *)malloc(sizeof(struct PlaylistNode));
    newNode->song_id = song_id;
    newNode->next = NULL;

    if (head == NULL) {
        newNode->prev = NULL;
        head = newNode;
    } else {
        struct PlaylistNode* temp = head;
        while (temp->next != NULL) {
            temp = temp->next;
        }
        temp->next = newNode;
        newNode->prev = temp;
    }
    printf("Song ID %d added to custom playlist.\n", song_id);
}

// 4. Category Tree for Genres (Binary Search Tree) - CO3
struct GenreNode {
    int genre_id;
    struct GenreNode* left;
    struct GenreNode* right;
};

struct GenreNode* create_genre_node(int id) {
    struct GenreNode* newNode = (struct GenreNode*)malloc(sizeof(struct GenreNode));
    newNode->genre_id = id;
    newNode->left = newNode->right = NULL;
    return newNode;
}

struct GenreNode* insert_genre(struct GenreNode* root, int id) {
    if (root == NULL) return create_genre_node(id);
   
    if (id < root->genre_id)
        root->left = insert_genre(root->left, id);
    else if (id > root->genre_id)
        root->right = insert_genre(root->right, id);
       
    return root;
}

// Helper function to display BST exactly as shown in Stage 2 Output A
void inorder_traversal(struct GenreNode* root) {
    if (root != NULL) {
        inorder_traversal(root->left);
        printf("%d ", root->genre_id);
        inorder_traversal(root->right);
    }
}

// 5. Search by Song ID (Hash Table) - CO4
#define TABLE_SIZE 10
int hashTable[TABLE_SIZE];

void init_hash_table() {
    for(int i = 0; i < TABLE_SIZE; i++) hashTable[i] = -1;
}

void insert_hash(int song_id) {
    int index = song_id % TABLE_SIZE;
    while (hashTable[index] != -1) {
        index = (index + 1) % TABLE_SIZE;
    }
    hashTable[index] = song_id;
}

int search_hash(int song_id) {
    int index = song_id % TABLE_SIZE;
    int start_index = index;
   
    while (hashTable[index] != -1) {
        if (hashTable[index] == song_id) return 1;
        index = (index + 1) % TABLE_SIZE;
        if (index == start_index) break;
    }
    return 0;
}

// Main Function Replicating PDF Executions
int main(int argc, char** argv) {
    printf("--- Stage 1 Executions ---\n");
    // Stage 1 Output A
    enqueue_song(101);
    enqueue_song(102);
    insert_playlist(505);
   
    // Stage 1 Output B (Simulated Hash Lookup)
    init_hash_table();
    insert_hash(33);
    printf("Searching for Song ID 33...\n");
    if(search_hash(33) == 1) {
        // Hash for 33 in size 10 is 3
        printf("Result: Song Found at Hash Index 3!\n");
    }

    printf("\n--- Stage 2 Executions ---\n");
    // Stage 2 Output A (BST In-order Traversal)
    struct GenreNode* root = NULL;
    root = insert_genre(root, 40);
    insert_genre(root, 20);
    insert_genre(root, 30);
    insert_genre(root, 70);
    insert_genre(root, 50);
    insert_genre(root, 80);
   
    printf("Output A:\nIn-order Traversal of Genre IDs:\n");
    inorder_traversal(root);
    printf("\n\n");
   
    // Stage 2 Output B (Hash Search Results)
    printf("Output B:\n");
    init_hash_table(); // Reset table for clean state
    insert_hash(14);
    insert_hash(34);
   
    printf("Search for song 14 (Expected 1): %d\n", search_hash(14));
    printf("Search for song 34 (Expected 1): %d\n", search_hash(34));
    printf("Search for song 99 (Expected 0): %d\n", search_hash(99));
   
    return EXIT_SUCCESS;
}
