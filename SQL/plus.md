# 외래키의 부모요소 삭제시 자식요소도 삭제
  * 외래키 지정시 끝에 ON DELETE CASCADE 를 추가 해준다.
    ```sql
    ALTER TABLE [테이블명] ADD CONSTRAINT [외래키명] FOREIGN KEY[컬럼명] references [기준 테이블] (기본키 컬럼명) on delete cascade;

    -- 예시
    ALTER TABLE genre_tbl ADD CONSTRAINT fk_game_code_genre FOREIGN KEY(game_code) references game_tbl (game_code) on delete cascade;
    ```

# 외래키 무시하고 테이블 삭제
  * 테이블을 삭제할 때 CASCADE CONSTRAINTS를 추가한다.
    ```sql
    DROP TABLE [테이블명] CASCADE CONSTRAINTS;
    ```